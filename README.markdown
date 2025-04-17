# Sonoff 2-Button Light Switch with Sunrise/Sunset

This ESPHome configuration enables a Sonoff 2-button (e.g., T2 2-gang) touch switch to control Garage and Carport Lights, with the Carport Light automatically toggling at sunrise and sunset. It integrates with Home Assistant for local control and monitoring.

## Features

- **Light Control**: Garage Lights (GPIO12) and Carport Lights (GPIO5) controlled via relays.
- **Button Input**: Buttons on GPIO0 (Button 1) and GPIO9 (Button 2) toggle Garage and Carport Lights, respectively.
- **Sunrise/Sunset Automation**: Carport Light turns on at sunset (e.g., 18:03 SAST) and off at sunrise (e.g., 06:09 SAST) based on your location.
- **Blue LED**: Indicates Garage Lights state via GPIO13.
- **Monitoring**: Reports WiFi signal (dB and %), uptime, and next sunrise/sunset times.
- **Home Assistant Integration**: Exposes lights, buttons, and sensors via the Home Assistant API.

## Prerequisites

- Sonoff 2-button touch switch (e.g., T2 2-gang, ESP8266-based)
- ESPHome (installed and configured with Home Assistant)
- Home Assistant for integration
- Latitude and longitude of your location (e.g., Benoni, Gauteng: -26.1885, 28.3208)
- USB-to-serial adapter (3.3V) for initial flashing
- Basic wiring tools (if modifying connections)

## Hardware Setup

1. **Power and Wiring**:
   - Wire the Sonoff to mains power (100-240V AC) per its manual:
     - **L**: Live input
     - **N**: Neutral
     - **L1**: Load for Garage Lights (GPIO12 relay)
     - **L2**: Load for Carport Lights (GPIO5 relay)
   - Buttons (GPIO0, GPIO9) and blue LED (GPIO13) are internal; no external wiring needed.

2. **Safety**:
   - Disconnect power during wiring to avoid electrical shock.
   - Ensure the Sonoff’s relays are rated for your light loads (max 2A per channel).

## Installation

1. **Clone the Repository**:

   ```bash
   git clone <repository-url>
   ```

2. **Configure ESPHome**:

   - Copy `garage-lights.yaml` to your ESPHome configuration directory.
   - Update secrets in `secrets.yaml`:

     ```yaml
     wifi_ssid: "YourWiFiSSID"
     wifi_password: "YourWiFiPassword"
     api_encryption_key: "YourAPIKey"
     fallback_password: "YourFallbackPassword"
     home_latitude: "-26.1885"  # e.g., Benoni, Gauteng
     home_longitude: "28.3208"
     ```

3. **Flash the Firmware**:

   - Connect the Sonoff to a USB-to-serial adapter:
     - VCC → 3.3V
     - GND → GND
     - RX → TX (Sonoff)
     - TX → RX (Sonoff)
     - Hold GPIO0 (or touch button) while powering on to enter flash mode.
   - Compile and upload:

     ```bash
     esphome run garage-lights.yaml
     ```

   - Use OTA for subsequent updates.

4. **Add to Home Assistant**:

   - The device appears in Home Assistant under `Settings > Devices & Services > ESPHome`.
   - Add it to expose entities: `Garage Lights`, `Carport Lights`, `Button 1`, `Button 2`, `WiFi signal`, `Uptime`, `Next Sunrise`, `Next Sunset`.

## Usage

- **Manual Control**: Tap Button 1 to toggle Garage Lights; tap Button 2 to toggle Carport Lights. Control lights via Home Assistant.
- **Sunrise/Sunset**: Carport Light automatically turns on at sunset and off at sunrise, based on your location’s coordinates.
- **Blue LED**: Lights up when Garage Lights are on.
- **Monitoring**: Check `WiFi signal` for network health, `Uptime` for reboot detection, and `Next Sunrise`/`Next Sunset` for sun times.

## Troubleshooting

- **Carport Light Not Toggling at Sunrise/Sunset**:
  - Verify `home_latitude` and `home_longitude` in `secrets.yaml`.
  - Check ESPHome logs (`esphome logs garage-lights.yaml`) for sun event triggers.
  - Ensure Home Assistant’s time zone is set to SAST (Africa/Johannesburg).
- **Frequent Reboots**:
  - Per your April 16, 2025 issue, monitor `Uptime` in Home Assistant. Check power supply stability and reduce relay load if needed.
  - If reboots persist, inspect logs for errors (e.g., watchdog timeouts).
- **Device Unavailable**:
  - Per your April 4, 2025 issue, restart the Sonoff and Home Assistant. Verify WiFi signal strength in Home Assistant.
- **Button Issues**:
  - Ensure touch sensors are clean. Check GPIO0/GPIO9 configuration if buttons don’t respond.

## Notes

- **Timing Precision**: Uses ESPHome’s `sun` component for accurate sunrise/sunset triggers, addressing your April 16, 2025 issue with `below_horizon` imprecision (e.g., 19:48 SAST triggers).
- **Reboot Prevention**: Minimal configuration and `uptime` sensor reduce bloat and track reboots, per your April 16, 2025 concerns.
- **Blue LED**: Linked to Garage Lights state, not exposed as a separate entity, per your April 17, 2025 preference.

## Contributing

Submit issues or pull requests for improvements, such as additional sensors or refined automation logic.

## License

MIT License