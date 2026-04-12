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

### Electrical & Hardware Safety

This project involves a LiPo battery and a custom PCB. Follow these precautions to avoid damaging hardware or causing a fire:

**LiPo battery**
- **Always observe correct polarity** when connecting the battery — reverse polarity will likely destroy the LOLIN board and may cause a fire or explosion
- Do not charge an unattended LiPo battery — the LOLIN S3 Pro has a built-in TP4054/compatible charger, which is rated for up to the board's maximum charge current; use an appropriately rated cell (minimum 500 mAh)
- Do not use a damaged, swollen, or punctured LiPo — dispose of it safely
- Store at approximately 50% charge if unused for an extended period

**PCB — AO3401 P-MOSFET (Q1)**
- The P-channel MOSFET on the custom PCB acts as a high-side power switch between the battery and the LOLIN board
- Its body diode blocks reverse current flow from the board back into the battery in unexpected conditions, providing a basic level of protection
- However, the PCB does **not** include a dedicated reverse polarity protection circuit — connecting the battery with reversed polarity at the screw terminals will bypass the MOSFET's protection and may damage components
- The 10 kΩ gate pull resistor (R1) ensures the MOSFET stays OFF when no control signal is applied, preventing accidental power-on

**Slide switch (SS-12D10G5 — U3)**
- This switch is wired to GPIO2 only — it controls whether the firmware allows deep sleep, not battery power
- The battery remains connected at all times regardless of switch position
- There is no hardware power cut-off; to fully disconnect the battery, unplug it from the screw terminal (PCB) or the JST connector (LOLIN board)

**Reverse polarity protection — without PCB**
- When connecting the battery **directly to the LOLIN S3 Pro** (without the custom PCB), there is no dedicated reverse polarity protection
- The AO3401 MOSFET on the PCB provides incidental protection via its body diode; without the PCB this is absent
- Always verify polarity before connecting the battery — reversed polarity will likely destroy the charging IC and/or the ESP32-S3

**ADC input (GPIO3)**
- The battery voltage divider (×2) is designed for a single-cell LiPo range (3.0–4.2 V at the cell = up to 4.2 V into the ADC after division)
- Do **not** connect voltages above 3.3 V directly to GPIO3 without the divider — ESP32-S3 GPIO pins are not 5 V tolerant

### What This Project Does NOT Do

- It does **not** store any personal data beyond what's displayed on screen
- It does **not** transmit data to any external service — all communication is local (ESP32 ↔ Home Assistant)
- It does **not** expose any network ports — it only initiates outbound connections to your HA instance
