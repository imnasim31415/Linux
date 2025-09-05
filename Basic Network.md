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
