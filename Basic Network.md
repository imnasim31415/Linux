# How DNS is resolved:

```
 ┌───────────────────────────┐
 │       Your Program        │
 │   (browser, ping, etc.)   │
 └─────────────┬─────────────┘
               │  "hostname or domain?"
               ▼
   ┌───────────────────────────────┐
   │     Check /etc/hosts          │
   │     (local static entries)    │
   └─┬─────────────────────────┬───┘
     │ Match!                  │ No match
     │                         │
     ▼                         ▼
 Answer returned        ┌───────────────────────────┐
 immediately            │ Local Stub Resolver       │
 (localhost, myapp.com  │ systemd-resolved          │  --> systemctl status systemd-resolved.service 
 printer=192.168.0.111) │ listens on 127.0.0.53     │  --> This is found at /etc/resolve.conf.
                        └─────────────┬─────────────┘  
                                      │
                                      ▼
                        ┌───────────────────────────┐
                        │ Router / Default Gateway  │
                        │ LAN IP: 192.168.0.1       │
                        │ - Acts as DNS forwarder   │
                        │ - Knows DHCP hostnames    │
                        └───┬───────────────────────┘
                            │
             ┌──────────────┴───────────────┐
             │ Match                        │  No match
             ▼                              ▼
   Local LAN name found            Not in LAN (needs internet)
   e.g. "pc2" → 192.168.0.112      ex "dsi.com"
   Router returns local IP         ┌───────────────────────────┐
                                   │ ISP/Public DNS Servers    │
                                   │ example 8.8.8.8 / 1.1.1.1 │
                                   │ Queries root → TLD → auth │
                                   └─────────────┬─────────────┘
                                                 │
                                                 ▼
                                   Answer returned from Internet


```


# Setting a Static IP using *nmcli*

## Current Setup

```
Interface: wlp0s20f3
SSID: Tenda@imnasim3.1415
Current IP: 192.168.0.110/24 (dynamic)
Gateway: 192.168.0.1
```

## Steps

### 1. Identify Wi-Fi connection

```bash
nmcli connection show
```

### 2. Configure static IP

```bash
nmcli con mod "Tenda@imnasim3.1415" ipv4.addresses 192.168.0.50/24
nmcli con mod "Tenda@imnasim3.1415" ipv4.gateway 192.168.0.1
nmcli con mod "Tenda@imnasim3.1415" ipv4.dns 8.8.8.8
nmcli con mod "Tenda@imnasim3.1415" ipv4.method manual
```

### 3. Apply changes

```bash
nmcli con down "Tenda@imnasim3.1415"
nmcli con up "Tenda@imnasim3.1415"
```

### 4. Verify configuration

```bash
ip addr show wlp0s20f3     # Confirm IP
ip route                   # Confirm gateway
nmcli device show wlp0s20f3 # Confirm DNS and method
```

## Things to remember

* **Pick an IP outside the DHCP pool** of your router.
* **Check the subnet mask** (`/24` = 255.255.255.0) to match your LAN.
* **Set the correct default gateway** (usually your router’s IP).
* **Specify at least one reliable DNS** (e.g., 8.8.8.8, 1.1.1.1).
* **Persist configuration** via NetworkManager; do not rely on temporary `ip addr add`.

## Optional: Using `nmtui` (Text UI)

1. Run `nmtui`.
2. Select **Edit a connection**.
3. Choose your Wi-Fi SSID → **Edit** → Set **IPv4 Configuration** to `Manual`.
4. Enter IP address, gateway, and DNS.
5. Save and **Activate** the connection.

---



# Different tools used

| Task / Info Needed                     | `netstat` (old)                | `ss` (modern)                     | `nmcli` (NetworkManager CLI)                        | `nmtui` (TUI)                     |
|---------------------------------------|--------------------------------|---------------------------------|----------------------------------------------------|----------------------------------|
| **Show listening TCP ports**           | `netstat -tln`                 | `ss -tln`                       | `nmcli device show` (IP & connection info)        | Interactive view under “Edit Connection” |
| **Show listening UDP ports**           | `netstat -uln`                 | `ss -uln`                       | —                                                  | —                                |
| **Show all connections (TCP/UDP)**     | `netstat -an`                  | `ss -a`                          | —                                                  | —                                |
| **Show established TCP connections**   | `netstat -at`                  | `ss -t state established`        | —                                                  | —                                |
| **Show process owning socket**         | `netstat -tulpn`               | `ss -lptn`                       | —                                                  | —                                |
| **Show routing table**                 | `netstat -rn`                  | `ip route`                        | `nmcli device show` (default gateway info)        | —                                |
| **Show network interfaces & IPs**     | `ifconfig -a` (deprecated)    | `ip addr`                         | `nmcli device show`                                | “Edit Connection” / “Activate Connection” |
| **Bring connection up/down**           | `ifup/ifdown <iface>`          | `ip link set <iface> up/down`     | `nmcli con up <name>` / `nmcli con down <name>`   | “Activate a connection” menu     |
| **Connect to Wi-Fi**                   | —                              | —                                 | `nmcli dev wifi connect <SSID> password <PWD>`    | “Activate a connection” / Wi-Fi list |
| **Set static IP/DNS**                  | —                              | —                                 | `nmcli con mod <name> ipv4.address ...`          | “Edit Connection”                |
| **Interactive menu for humans**       | —                              | —                                 | —                                                  | `nmtui`                          |

---

### Notes
- **`ss`** is faster, more detailed, and recommended over `netstat`.  
- **`nmcli`** and **`nmtui`** only work if your Linux uses **NetworkManager** (Ubuntu, Fedora, Pop!_OS, etc.).  
- **`netstat`** may still exist on older servers or minimal distros (`net-tools` package).  
- For scripting and automation: **`nmcli` + `ss`** is your best combo.
