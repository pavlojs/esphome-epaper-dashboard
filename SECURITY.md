# Security Policy

## Reporting Vulnerabilities

If you discover a security vulnerability in this project, please report it responsibly:

1. **Do NOT open a public GitHub issue** for security vulnerabilities
2. Use [GitHub Security Advisories](https://github.com/pavlojs/esphome-epaper-dashboard/security/advisories/new) to report privately
3. Alternatively, contact the maintainer directly via the email listed on the GitHub profile

You should receive a response within 7 days. The vulnerability will be addressed and disclosed responsibly once a fix is available.

## Supported Versions

| Version | Supported |
|---------|-----------|
| `main` branch (latest) | ✅ Yes |
| Older commits | ❌ No |

## IoT Security Best Practices

This project runs on an ESP32 microcontroller connected to your home network and Home Assistant. Follow these guidelines to keep your setup secure:

### WiFi & Network

- **Always use `secrets.yaml`** for WiFi credentials and API keys — never hardcode them in `dashboard.yaml`
- **Isolate IoT devices** on a separate VLAN or WiFi network if your router supports it
- Use **WPA3** or at minimum **WPA2** for your WiFi network
- **Disable the AP fallback hotspot** in production, or set a strong password if you keep it enabled

### ESPHome & Home Assistant

- **Keep ESPHome up to date** — security patches are released regularly
- **Keep Home Assistant up to date** — same reason
- **Use the encrypted native API** (default in ESPHome) — do not downgrade to plaintext
- **Set a unique OTA password** to prevent unauthorized firmware updates:
  ```yaml
  ota:
    - platform: esphome
      password: "your-strong-ota-password"
  ```
- **Review entity exposure** — the dashboard only needs read access to specific sensors; avoid exposing sensitive entities

### Physical Security

- If the device is in a publicly accessible location, be aware that the USB-C port allows reflashing
- The deep sleep switch (GPIO2) can be toggled physically — consider placement carefully

### What This Project Does NOT Do

- It does **not** store any personal data beyond what's displayed on screen
- It does **not** transmit data to any external service — all communication is local (ESP32 ↔ Home Assistant)
- It does **not** expose any network ports — it only initiates outbound connections to your HA instance
