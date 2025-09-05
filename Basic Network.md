## How DNS is resolved:

```
 ┌───────────────────────────┐
 │       Your Program        │
 │   (browser, ping, etc.)   │
 └─────────────┬─────────────┘
               │  "hostname or domain?"
               ▼
 ┌───────────────────────────┐
 │ Check /etc/hosts          │
 │ (local static entries)    │
 └─────────────┬─────────────┘
     │ Match?                  │ No match
     │                         │
     ▼                         ▼
 Answer returned        ┌───────────────────────────┐
 immediately            │ Local Stub Resolver       │
 (e.g. localhost,       │ systemd-resolved          │
 printer=192.168.0.111) │ listens on 127.0.0.53     │  --> This is found at /etc/resolve.conf.
                        └─────────────┬─────────────┘  
                                      │
                                      ▼
                        ┌───────────────────────────┐
                        │ Router / Default Gateway  │
                        │ LAN IP: 192.168.0.1       │
                        │ - Acts as DNS forwarder   │
                        │ - Knows DHCP hostnames    │
                        └─────────────┬─────────────┘
                            │
             ┌──────────────┴───────────────┐
             │                              │
             ▼                              ▼
   Local LAN name found            Not in LAN (needs internet)
   e.g. "pc2" → 192.168.0.112      e.g. "dsi.com"
   Router returns local IP         ┌───────────────────────────┐
                                   │ ISP/Public DNS Servers    │
                                   │ e.g., 8.8.8.8 / 1.1.1.1   │
                                   │ Queries root → TLD → auth │
                                   └─────────────┬─────────────┘
                                                 │
                                                 ▼
                                   Answer returned from Internet


```
