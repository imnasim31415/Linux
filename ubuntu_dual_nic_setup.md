
# 🖧 Dual-NIC Ubuntu Server VM: Static + Dynamic IP Setup (With Internet + Host Access)

When working with virtualized Ubuntu Server VMs, it's common to need **internet access** as well as **communication with the host or other VMs**. This is cleanly solved by using **two network interfaces (NICs)**:

- One using **NAT** with **DHCP (dynamic IP)** for internet
- Another using **Host-only Adapter** with a **static IP** for local networking

---

## 🧩 Setup Summary

### 💡 VM Network Adapters

| NIC   | Interface | Type       | IP Type | Purpose                     |
|-------|-----------|------------|---------|-----------------------------|
| NIC 1 | `enp0s3`  | NAT        | DHCP    | Internet access             |
| NIC 2 | `enp0s8`  | Host-only  | Static  | Host ↔ VM / VM ↔ VM traffic |

---

## ⚙️ Netplan Configuration (`/etc/netplan/50-cloud-init.yaml`)

```yaml
network:
  version: 2
  ethernets:
    enp0s3:
      dhcp4: true
    enp0s8:
      dhcp4: no
      addresses:
        - 192.168.123.104/24
      nameservers:
        addresses:
          - 8.8.8.8
```

> ⚠️ **Do not add `gateway4` or `routes` to `enp0s8`**, or you might lose internet connectivity. Only `enp0s3` should handle the default route.

### Apply Changes

```bash
sudo netplan apply
```

Or use `try` for a test-safe approach:

```bash
sudo netplan try
```

---

## 🧪 Verify Routing

Use the following to confirm your route setup:

```bash
ip route
```

Expected output (simplified):

```
default via 10.0.2.2 dev enp0s3
192.168.123.0/24 dev enp0s8 proto kernel scope link src 192.168.123.104
```

---

## 🛠 Common Pitfalls

- Don't set multiple default routes.
- Ensure `enp0s8` doesn't use DHCP to avoid IP conflicts.
- File permission warning? Fix with:
  ```bash
  sudo chmod 600 /etc/netplan/*.yaml
  ```

---

## 🧘 Result

You get:
- ✅ Full internet access via NAT (`enp0s3`)
- ✅ Static IP for local development, host communication, or inter-VM networking (`enp0s8`)

