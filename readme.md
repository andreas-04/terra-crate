# TerraCrate File Share

TerraCrate is a self hosted file sharing NAS solution for a Raspberry Pi

## Quick Start

```bash
chmod +x setup.sh
sudo ./setup.sh
```

The setup script will:

1. Generate a `.env` file if one does not already exist.
2. Prompt for the **mDNS hostname** — defaults to the current system hostname, or lets you enter a new one.
3. Prompt for the **initial admin password** (stored as `ADMIN_PIN` in `.env`).
4. Prompt for a **WiFi AP passphrase** (8–63 characters). This is used if the Pi cannot join a known network on boot — it will fall back to broadcasting a `TerraCrate-AP` access point.
5. Install `avahi-daemon`, `hostapd`, and `dnsmasq`, and configure mDNS so the device is discoverable on your LAN at `https://<hostname>.local`.
6. Update the system hostname if you chose a new one.
7. Optionally **LUKS-encrypt** a secondary storage drive (you will be prompted if additional drives are detected). The drive auto-unlocks on boot via a keyfile stored on the SD card.
8. Install and start `terracrate.service` — a systemd unit that automatically manages `docker compose` on boot. No need to run `docker compose` manually.

Once setup completes, navigate to:

```
https://<hostname>.local
```

Login via:
- **username**: `admin@<hostname>.local`
- **password**: the `ADMIN_PIN` you set during setup

You will be asked to change your password after initial login.

## Customizing the mDNS Hostname

By default, your device is advertised as `terracrate.local`. To use a different hostname:

1. Edit `.env` in the project root and update `MDNS_HOSTNAME`:
   ```
   MDNS_HOSTNAME=mydevice
   ```
2. Update the system hostname:
   ```bash
   sudo hostnamectl set-hostname mydevice
   ```
3. Update `/etc/hosts` so that `127.0.1.1` resolves to the new hostname:
   ```bash
   sudo sed -i "s/127\.0\.1\.1.*/127.0.1.1\tmydevice/" /etc/hosts
   ```
4. Restart the services:
   ```bash
   sudo systemctl restart avahi-daemon
   sudo systemctl restart terracrate
   ```

Your device will then be discoverable at `https://mydevice.local`.

