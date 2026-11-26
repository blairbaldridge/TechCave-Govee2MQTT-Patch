# Setup

1. Back up Home Assistant and record the original Govee2MQTT configuration privately.
2. Install this app, but do not start it yet. The local Rust build can be slow on ARM.
3. Copy the original app's configuration values into this app. Supply your Govee account email/password for shortcut discovery, API key if configured, and the same MQTT settings. Do not commit credentials to GitHub.
4. Stop the original app; disable its Start on boot and Watchdog. Never run both against the same broker at once.
5. Start this app and inspect its log. Look for successful device enumeration and the absence of `Failed to parse one-clicks`. Search Home Assistant Scenes for your existing Tap-to-Run shortcuts and test a safe scene. Test normal lights too.
6. If necessary, use the Govee to MQTT device's Purge Caches button once. Avoid repeatedly restarting/purging if you see 429 rate limiting.
7. Only after verification enable Start on boot and Watchdog on the patched app.

The app uses the original MQTT discovery implementation. Matching broker/account/configuration should preserve IDs, but this is not guaranteed. Do not delete entities during troubleshooting. App storage/cache is separate; any manually configured files in the original app's mapped configuration folder are not migrated automatically.

## Rollback
Stop this app, disable its Start on boot/Watchdog, then start the original app and restore its boot/watchdog settings. Keep the old app installed until the official fix is confirmed. No MQTT or Govee account changes are required.

## Troubleshooting
The fix addresses shortcut authentication, not all native DreamView features or unsupported TV backlight capabilities. If `app2.govee.com` fails DNS too, troubleshoot network/DNS. If 401 persists, confirm credentials and check upstream PR #739; a response with HTTP 200 and a JSON 401 may still require a future fix. Never post raw logs containing account details, device IDs, or tokens.
