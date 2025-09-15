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
---

# PC1 ↔ PC2 (same LAN)
```
PC1 (192.168.1.10)                  PC2 (192.168.1.11)
        │                                    │
        │--- ARP: Who has 192.168.1.11? ---->│
        │<-- ARP Reply: MAC of PC2 ----------│
        │                                    │
        │------ Data Packet ---------------->│
        │   Src IP: 192.168.1.10             │
        │   Dst IP: 192.168.1.11             │
        │   Src MAC: PC1                     │
        │   Dst MAC: PC2                     │
        │                                    │
       (Switching inside router, no NAT, no routing)
```
✅ Direct layer-2 switch forwarding. Very fast, no NAT, no firewall involved.

# PC1 ↔ Internet (via WAN + NAT)
```
PC1 (192.168.1.10)           Router (192.168.1.1 LAN / Public_IP WAN)       ISP / Internet
        │                                │                                      │
        │--- Packet to Google ---------->│                                      │
        │   Src IP: 192.168.1.10         │                                      │
        │   Dst IP: 142.250.x.x          │                                      │
        │   Next Hop: 192.168.1.1        │                                      │
        │                                │                                      │
        │                                │--- NAT Translation ----------------->│
        │                                │   Src IP changed to Public_IP        │
        │                                │   Dst IP: 142.250.x.x                │
        │                                │                                      │
        │                                │---------------------> Google Server  │
        │                                │                                      │
        │                                │<--------------------- Google Reply   │
        │                                │   Dst IP: Public_IP                  │
        │                                │                                      │
        │<--------- NAT Reverse ----------│                                      │
        │   Dst IP changed back: 192.168.1.10                                   │
        │   Src IP: 142.250.x.x                                                │
        │                                │                                      │
        │------- Reply Received -------->│                                      │
```

✅ Here the router does routing + NAT so internal private IPs can reach the public Internet.


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

### Notes
- **`ss`** is faster, more detailed, and recommended over `netstat`.  
- **`nmcli`** and **`nmtui`** only work if your Linux uses **NetworkManager** (Ubuntu, Fedora, Pop!_OS, etc.).  
- **`netstat`** may still exist on older servers or minimal distros (`net-tools` package).  
- For scripting and automation: **`nmcli` + `ss`** is your best combo.

---

# FirewallD 

| Scenario | Command | Description |
|----------|---------|-------------|
| Check firewalld status | `sudo firewall-cmd --state` | Shows if firewalld is running. |
| List active zones | `sudo firewall-cmd --get-active-zones` | Displays zones currently in use and their interfaces. |
| Show default zone | `sudo firewall-cmd --get-default-zone` | Displays the system’s default zone. |
| Change default zone | `sudo firewall-cmd --set-default-zone=public` | Sets the system’s default zone. |
| List all available zones | `sudo firewall-cmd --get-zones` | Shows all predefined zones. |
| Show settings of a zone | `sudo firewall-cmd --zone=public --list-all` | Lists services, ports, and rules in a zone. |
| Assign interface to zone | `sudo firewall-cmd --zone=public --change-interface=eth0` | Temporarily assign an interface to a zone. |
| Allow service | `sudo firewall-cmd --zone=public --add-service=http` | Temporarily allow a service (HTTP). |
| Remove service | `sudo firewall-cmd --zone=public --remove-service=http` | Temporarily block a service. |
| List services | `sudo firewall-cmd --get-services` | Shows all services firewalld can manage. |
| Open port | `sudo firewall-cmd --zone=public --add-port=8080/tcp` | Temporarily open a specific TCP/UDP port. || Remove port | `sudo firewall-cmd --zone=public --remove-port=8080/tcp` | Temporarily block a port. |
| Allow IP range | `sudo firewall-cmd --zone=public --add-source=192.168.1.0/24` | Allows traffic from a specific IP range. |
| Add port forwarding | `sudo firewall-cmd --zone=public --add-forward-port=port=2222:proto=tcp:toport=22:toaddr=192.168.0.101` | Forward traffic from one port to another host/port. |
| Reload firewalld | `sudo firewall-cmd --reload` | Apply permanent changes without restarting firewall. |
| Reset all rules | `sudo firewall-cmd --complete-reload` | Resets firewalld rules and reloads defaults. |

Add `--permanent` to make the change persistent 


## **Zones Overview**

| Zone     | Trust Level  | Behavior                                                  |
|----------|--------------|-----------------------------------------------------------|
| drop     | Lowest       | Drops all incoming, no reply; only outgoing allowed.       |
| block    | Very low     | Rejects incoming with ICMP error; only outgoing allowed.   |
| public   | Low          | For use in public areas; only selected services allowed.   |
| external | For NAT gateways | Similar to public, but enables masquerading.           |
| internal | More trusted | For inside networks; more services allowed.                |
| dmz      | Semi-trusted | For servers in DMZ; only specific services open.           |
| work     | Medium trust | For work machines; more services like SSH/Samba allowed.   |
| home     | Higher trust | For home networks; many services allowed.                  |
| trusted  | Highest      | Accept all connections.                                    |



---
# timedatectl

| Task | Command |
|------|---------|
| Check time & sync status | `timedatectl status` |
| List available timezones | `timedatectl list-timezones` |
| Set timezone | `sudo timedatectl set-timezone Asia/Dhaka` |
| Set date & time manually | `sudo timedatectl set-time "2025-09-07 14:30:00"` |
| Enable NTP sync | `sudo timedatectl set-ntp true` | 
| Enable RTC sync (Using Local CMOS) | `sudo timedatectl set-local-rtc true`|
| Disable RTC sync (Using UTC) | `sudo timedatectl set-local-rtc false` (recomended) |

# chronyd / chronyc

| Task | Command |
|------|---------|
| Install chrony | `sudo yum install chrony -y` (RHEL/CentOS)|
| Check chronyd status | `sudo systemctl status chronyd` |
| Show connected clients (server mode) | `chronyc clients` |
| Configuration file | `/etc/chrony.conf` |




✅ **Quick tip**:  
- Use **`timedatectl`** for **basic system time, timezone, and enabling/disabling NTP**.  
- Use **`chronyc`** to **query and control the running `chronyd` service**.  
- Use **`systemctl`** to **start/stop/enable `chronyd`**.  



---
# Router IP Scenarios and Flows
## **Scenario 1: Single PC Browsing the Internet**

**Context:**

* PC → `192.168.0.101`
* Router LAN → `192.168.0.1`
* Router WAN → `203.0.113.25`
* Website → `93.184.216.34`

```
PC (192.168.0.101)
   → sends packet to router LAN IP (192.168.0.1)
   → router translates to WAN IP (203.0.113.25)
   → Internet (93.184.216.34)

Reply:
Internet (93.184.216.34)
   → to router WAN IP (203.0.113.25)
   → router rewrites to LAN IP (192.168.0.101)
   → PC gets reply
```

**Explanation:**
The PC never talks directly to the Internet. It always hands traffic to the router’s **LAN IP**. The router’s WAN IP is what the Internet sees. NAT keeps track of which device made the request.

---

## **Scenario 2: Two PCs Browsing the Internet**

**Context:**

* PC1 → `192.168.0.101`
* PC2 → `192.168.0.102`
* Router LAN → `192.168.0.1`
* Router WAN → `203.0.113.25`

```
LAN Side                          WAN Side

PC1 192.168.0.101 ----\
                      |      Router LAN: 192.168.0.1
PC2 192.168.0.102 ----+----> Router WAN: 203.0.113.25 ----> Internet
```

**Explanation:**
Both PCs share the same public IP (`203.0.113.25`). NAT keeps per-connection mappings so replies from the Internet are correctly forwarded back to the right PC.

---

## **Scenario 3: Two PCs Communicating Locally**

**Context:**

* PC1 → `192.168.0.101`
* PC2 → `192.168.0.102`
* Same LAN, same subnet.

```
PC1 (192.168.0.101) ──────► PC2 (192.168.0.102)
```

**Explanation:**
When two devices are on the same subnet, they talk **directly** using ARP to resolve each other’s MAC address. The router is not involved.

---


## **Scenario 4: External Device Reaching a PC via Port Forwarding**

**Context:**

* PC → `192.168.0.101`
* Router LAN → `192.168.0.1`
* Router WAN → `203.0.113.25`
* External client wants to SSH into PC.

**Flow:**

```
External Client → 203.0.113.25:22
Router (NAT) → forwards to 192.168.0.101:22
PC responds → Router rewrites back to WAN → Internet
```

**Explanation:**
By default, incoming traffic from the Internet is blocked. Port forwarding allows the router to redirect specific ports from WAN to a LAN device.

---
## Scenario 5: NMCLI Routing for a System Interface

**Context:**

* System Interface → `eth0` → LAN 192.168.121.0/24
* Gateway for specific network → `172.28.128.100`
* Target Network → `192.168.0.0/24`

**Flow:**

```
System eth0 → 192.168.121.217
Traffic to 192.168.0.0/24 → routed via 172.28.128.100
Other traffic → follows default route
```

**Commands:**

```bash
# Add permanent route
sudo nmcli connection modify 'System eth0' +ipv4.routes "192.168.0.0/24 172.28.128.100"

# Apply changes immediately
sudo nmcli connection up 'System eth0'

# Verify route
ip route show
# 192.168.0.0/24 via 172.28.128.100 dev eth0 proto static

# Remove route if needed
sudo nmcli connection modify 'System eth0' -ipv4.routes "192.168.0.0/24 172.28.128.100"
sudo nmcli connection up 'System eth0'
```


# Summary

* Router has **two IPs**: LAN (internal) and WAN (external).
* PCs always use the **LAN IP** as gateway.
* NAT translates between LAN IPs and WAN IP.
* Direct LAN communication does not involve the router.
* Port forwarding allows external access into LAN.
* Bridge mode disables NAT, exposing devices directly to the Internet.
