# SSH: How it works — Detailed explanation

## Three main phases

When you run `ssh user@server` three big phases occur:

1. **Connection & Handshake** — negotiate protocol version, ciphers, and perform a key exchange (KEX).
2. **Authentication** — prove the client's identity (password or public-key challenge/response).
3. **Secure Session** — encrypted channel used for interactive shell, file transfer, port forwarding, etc.

---

## 1) Connection & Handshake (low-level)

**TCP connection:**

```
[Client] ---------------------> [Server:sshd]
           TCP SYN (connect)
```

**Protocol exchange & capability negotiation:** both sides exchange banners (e.g. `SSH-2.0-OpenSSH_8.7`) and then negotiate:

* Key-exchange algorithm (KEX): e.g. `diffie-hellman-group14-sha1`, `ecdh-sha2-nistp256`, `curve25519-sha256`
* Host key algorithm: `ssh-rsa`, `rsa-sha2-256`, `ecdsa-sha2-nistp256`, `ssh-ed25519`
* Symmetric cipher: `aes128-ctr`, `aes256-gcm@openssh.com`, `chacha20-poly1305@openssh.com`
* MAC / AEAD (integrity): HMAC-SHA2-256 or AEAD built into cipher

**Key exchange & host key verification (simplified):**

1. Client and server run a key-exchange protocol (e.g. ECDH) to derive a shared secret S.
2. The server signs the KEX exchange (exchange hash) with its **host private key**.
3. Client verifies that signature using the server's host public key (from `known_hosts` or first-time-accept).

This step prevents a classic MITM: without the server's host private key, an attacker cannot produce the signed KEX exchange value that matches the known host key.


```
+---------------------------+
| Key Exchange Negotiation  |
+---------------------------+
| Cipher chosen: AES-256    |
| KEX chosen: ECDH          |
| MAC chosen: HMAC-SHA256   |
+---------------------------+
```

**Result:** both sides derive identical symmetric session keys (encryption + integrity keys). All subsequent packets are encrypted.

---

## 2) Authentication phase

After the encrypted channel exists (the session keys), the client authenticates as a user. SSH supports many methods; the common ones are `password` and `publickey`.

### (a) Password

```
Client -> (encrypted) password -> Server
Server -> checks password (e.g. PAM, /etc/shadow)
```

Password authentication is conceptually simple but vulnerable to brute-force if not rate-limited or protected by additional controls.

### (b) Public-key (challenge–response) — how it works in detail

Public-key authentication avoids sending secrets over the wire. The server stores one or more **public keys** for a user in `~/.ssh/authorized_keys` and when that user attempts login the server issues a cryptographic challenge.

**Step-by-step:**

1. Client requests to authenticate as `alice`.
2. Server checks `~alice/.ssh/authorized_keys` for allowed public keys.
3. Server sends a challenge (a nonce / data blob) to the client.
4. Client uses the **private key** to create a signature over the challenge (plus some session-specific data) and returns the signature.
5. Server verifies the signature using the **public key** from `authorized_keys`.
6. If the signature verifies, authentication succeeds.


```
[Server] --- "Prove identity with your key" ---> [Client]
[Client] --- signs challenge with PRIVATE key --> [Server]
[Server] --- verifies with PUBLIC key ----------> success/fail
```

**Important:** the private key never leaves the client machine. The only thing transmitted is the signature, which proves possession of the private key.

---

## 3) Secure session — symmetric encryption & integrity

Once authentication succeeds both sides already have an agreed session key (from the KEX). The practical pattern is:

* Asymmetric crypto (KEX + host key + user key verification) is used to authenticate and *establish* shared secrets.
* Symmetric crypto (e.g. AES, ChaCha20) is used for **bulk encryption** of the interactive session — it's faster.
* A MAC or AEAD provides message integrity and authenticity.
* Rekeying: after a certain amount of data or time, SSH performs another KEX to rotate keys (forward secrecy and key freshness).

```
Client: plaintext command `ls -l`
 -> encrypt with symmetric session key
 -> send packet with MAC/AEAD
Server: receive -> verify MAC/AEAD -> decrypt -> execute command
```

**Forward secrecy note:** if KEX used ephemeral Diffie-Hellman (e.g. ECDH) then compromise of long-term private keys does not reveal past session keys.

---

# Full end-to-end ASCII flow (combined)

```
[Client]                                 [Server:sshd]
   |                                          |
   |----> TCP connect (port 22) -------------->|
   |<---  Server banner (SSH-2.0...) ---------|
   |----> Client banner ---------------------->|
   |
   |==== NEGOTIATION: Ciphers, MACs, KEX =====|
   | (KEX: ECDH/Curve25519; Hostkey: ssh-ed25519)
   |
   |<--- Server sends host public key --------|
   |----> Client verifies host key (known_hosts)
   |
   |==== Shared session key established ======|
   |
   |----> Request login as 'alice' ---------->|
   |<---  Challenge (nonce) ------------------|
   |----> Client signs challenge (private key)-|
   |----> Client sends signature ------------->|
   |<---  Server verifies using public key ---|
   |
   |------ If match = SUCCESS ----------------|
   |==== Secure channel ready =================|
   |  (All further data encrypted with AES/ChaCha)
```

---

# How keys are generated & file formats

**Generating keys with `ssh-keygen`:**

```bash
# Generate an RSA 4096-bit key
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -C "your_email@example.com"

# Generate Ed25519 (recommended for new keys)
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519 -C "your_email@example.com"
```

**Files produced:**

* `~/.ssh/id_rsa` (private key; keep secure) — historically PEM or OpenSSH private key format
* `~/.ssh/id_rsa.pub` (public key) — textual single-line format, e.g.:

```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC... user@example.com
```

**Public key format (one-liner):**

```
<key-type> <base64-key-data> <comment>
# e.g. ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIF... alice@laptop
```

**authorized\_keys:** the server stores public keys (one per line) in `~/.ssh/authorized_keys`. Each line can be prefixed by options like `from="host"`, `command="..."`, `no-pty`, `no-X11-forwarding`, etc.

**known\_hosts:** the client remembers server host keys in `~/.ssh/known_hosts` (or hashed entries). The first time you connect you may `Accept` and the host key is saved — subsequent connections verify the server’s identity against that saved key.

**Fingerprints:** use `ssh-keygen -l -f <file>` to show a fingerprint (SHA256 by default in modern OpenSSH). Fingerprints are how humans verify host keys.

---

## Cryptographic details (brief)

**RSA (very concise):**

* Keypair: public `(n, e)`, private `d` (n = p\*q)
* Signature (conceptual): `s = m^d mod n`; verification: `m' = s^e mod n`; check `m' == m`.

**Ed25519 / EdDSA (very concise):**

* Uses Edwards-curve signatures; smaller keys, faster, designed to be easy to use correctly.

**KEX / session key derivation (high level):**

* KEX (e.g. ECDH) yields shared secret `S` and an exchange hash `H`.
* Session keys are derived from `S` and `H` via a KDF per RFC 4253 (so both sides derive identical symmetric keys).
* The server signs `H` using its host private key and the client verifies that signature.

**Ciphers and AEAD:** modern SSH uses AEAD ciphers like `chacha20-poly1305` or `aes-gcm` which combine encryption and integrity.

---

## Practical & security notes

* **File permissions:** `~/.ssh` must be `700`, `~/.ssh/authorized_keys` should be `600`. Too-loose permissions cause OpenSSH to ignore keys.
* **Private key protection:** protect the private key with a passphrase when creating it. Use `ssh-agent` to cache unlocked keys in session.
* **Agent forwarding:** convenient but risky if you connect to untrusted servers — an attacker with root on the remote host could request signatures from your agent.
* **Revocation:** remove a public key from `authorized_keys` to revoke access for that key. For large deployments use SSH certificates and CA-based revocation alternatives.
* **Rate-limiting & Fail2ban:** protect password auth with rate-limiting, but key auth is preferred.

---

## Optional: quick commands & tips

```bash
# Copy public key to server (easy)
ssh-copy-id alice@server.example.com

# Manually install pubkey (if ssh-copy-id not available)
scp ~/.ssh/id_ed25519.pub alice@server:/tmp/pubkey
ssh alice@server
mkdir -p ~/.ssh && cat /tmp/pubkey >> ~/.ssh/authorized_keys
chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys

# View server host key fingerprint
ssh-keyscan -t ed25519 server.example.com | ssh-keygen -lf -
```

---

## Summary (concise)

* SSH uses asymmetric crypto to authenticate the server (host key) and optionally the user (public key auth), and symmetric crypto to encrypt bulk traffic.
* Public-key auth is secure because the private key never leaves the client and only signatures are transmitted.
* Proper key management (passphrases, agent, permissions, revocation) is essential for maintaining security.

---# SSH: Detailed Explanation & Cheatsheet

This document explains SSH end-to-end, including server/client configs, key management, authentication, and provides detailed guidance for a RHEL9 VM (IP: 192.168.123.105, user: tty.nasim).

---

#  `sshd_config`

* **Location:** `/etc/ssh/sshd_config`
* **Purpose:** configure the SSH daemon (server side).
* **Details:**

  * Determines how the server handles connections, authentication methods, logging, and security options.
  * You can define per-user or per-group rules using `Match` blocks.
  * Supports options like `AllowUsers` and `DenyUsers` to restrict login to specific accounts.
  * Controls whether root login is allowed and whether password or key-based authentication is required.
* **Important Notes:**

  * Always check syntax with `sshd -t` before restarting.
  * Changing ports can reduce automated attacks but requires firewall updates.
* **Reload after changes:**

```bash
sudo systemctl restart sshd
```

---

# `ssh_config`

* **Location:** `/etc/ssh/ssh_config` (system-wide) or `~/.ssh/config` (per-user)
* **Purpose:** configure SSH client behavior.
* **Details:**

  * You can define host-specific configurations using `Host` blocks.
  * Supports options for default user, identity files, proxy commands, port forwarding, and more.
  * Handy for creating aliases for multiple servers and automatically using correct keys.
* **Example connecting to RHEL9 VM:**

```ini
Host rhel9vm
    HostName 192.168.123.105
    User tty.nasim
    IdentityFile ~/.ssh/id_rsa
    Port 22
    ForwardAgent yes
```

* **Usage:**

```bash
ssh rhel9vm
```

* **Advanced:** Supports `ProxyJump`, `ServerAliveInterval`, and `ConnectTimeout` to improve reliability.

---

# AddressFamily

* **Purpose:** specify which IP version to use for SSH connections.
* **Options:**

  * `any` — use both IPv4 and IPv6 (default)
  * `inet` — IPv4 only
  * `inet6` — IPv6 only
* **Details:**

  * Useful when the client or server has multiple IP addresses.
  * Can help avoid connectivity issues when IPv6 is misconfigured.
* **Example for RHEL9 VM:**

```ini
AddressFamily inet
```

* Can be applied in both `sshd_config` and `ssh_config`.

---

# X11Forwarding

* **Purpose:** forward GUI applications over SSH from server to client.
* **Details:**

  * Requires an X server running on the client.
  * The server forwards GUI traffic over the encrypted SSH session.
  * Can be combined with `X11UseLocalhost yes/no` to control how the display is bound.
  * Display numbers can be offset using `X11DisplayOffset`.
* **Enable on server (`sshd_config`):**

```ini
X11Forwarding yes
X11DisplayOffset 10
X11UseLocalhost yes
```

* **Connect from client:**

```bash
ssh -X tty.nasim@192.168.123.105
```

* **Usage:** run GUI apps like `gedit` or `xclock` on server, display appears locally.
* **Security note:** X11 forwarding can be risky; only enable for trusted users.

---

# PasswordAuthentication

* **Purpose:** control whether SSH allows password login.
* **Details:**

  * If `yes`, users can log in using system passwords.
  * If `no`, only key-based or certificate-based authentication is allowed.
  * Recommended to disable in production for security.
  * Can be overridden in `Match` blocks for specific users.
* **Example for RHEL9 VM:**

```ini
PasswordAuthentication no  # enforce key-based login
```

* **Temporary enable for troubleshooting:**

```ini
PasswordAuthentication yes
```

* **Tip:** Combine with `PermitRootLogin no` to harden server.

---

# Match User

* **Purpose:** apply configuration rules for specific users, groups, hosts, or addresses.
* **Details:**

  * Only takes effect **after the `Match` line**; subsequent lines are applied conditionally.
  * Can change authentication methods, chroot directories, forced commands, etc.
* **Syntax:**

```ini
Match User <username>
    <option> <value>
```

* **Example for RHEL9 VM:**

```ini
Match User tty.nasim
    PasswordAuthentication no
    AllowTcpForwarding yes
```

* **Group example:**

```ini
Match Group sftpusers
    ChrootDirectory /sftp
    ForceCommand internal-sftp
```

* **Advanced:** Can combine multiple criteria:

```ini
Match User tty.nasim Address 192.168.123.0/24
    PermitTTY yes
```


# Comprehensive Cheatsheet Table

| Topic                  | Location                                 | Purpose                              | Key Options / Commands                                                   | Example (RHEL9 VM)                                                                      |
| ---------------------- | ---------------------------------------- | ------------------------------------ | ------------------------------------------------------------------------ | --------------------------------------------------------------------------------------- |
| `sshd_config`          | `/etc/ssh/sshd_config`                   | SSH server configuration             | Port, PermitRootLogin, PasswordAuthentication, X11Forwarding, Match User | `Port 22\nPermitRootLogin no\nPasswordAuthentication no`                                |
| `ssh_config`           | `/etc/ssh/ssh_config` or `~/.ssh/config` | SSH client configuration             | Host, HostName, User, IdentityFile                                       | `Host rhel9vm\n HostName 192.168.123.105\n User tty.nasim\n IdentityFile ~/.ssh/id_rsa` |
| AddressFamily          | `sshd_config` / `ssh_config`             | Select IP version                    | any, inet, inet6                                                         | `AddressFamily inet`                                                                    |
| X11Forwarding          | `sshd_config`                            | Enable GUI forwarding                | yes/no, X11DisplayOffset                                                 | `X11Forwarding yes\nX11DisplayOffset 10`                                                |
| PasswordAuthentication | `sshd_config`                            | Enable/disable password login        | yes/no                                                                   | `PasswordAuthentication no`                                                             |
| Match User             | `sshd_config`                            | Apply rules to specific users/groups | User, Group, Host, Address                                               | `Match User tty.nasim\n PasswordAuthentication no`                                      |


