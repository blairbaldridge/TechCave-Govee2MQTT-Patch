# TechCave Govee2MQTT Patch

Experimental, temporary Home Assistant OS/Supervised app for missing Govee Tap-to-Run shortcuts (including DreamView shortcuts saved as Tap-to-Run). Not an official Govee, Home Assistant or Govee2MQTT release.

## What it fixes

The old community login can fail DNS or produce a token no longer accepted by the shortcut API. This build uses the account-token fix proposed by **UPEngineer** in [upstream PR #739](https://github.com/wez/govee2mqtt/pull/739), for [issue #737](https://github.com/wez/govee2mqtt/issues/737). It builds the exact PR commit `5e60a5568bcc68eca774571c6644a1bebe7224fa` from the upstream GitHub repository. The main account token is used for shortcut discovery; this is not a workaround for account access controls. No additional Rust changes are claimed. No credentials are embedded.

## Install in Home Assistant

Supported targets: Raspberry Pi 5 running 64-bit HA OS (`aarch64`) and x86-64 (`amd64`) Supervisor installations. HA Container does not have an app store. The build is experimental and not yet verified on either target.

1. Create a Home Assistant backup.
2. Open **Settings → Apps → App store** (older versions: Add-ons → Add-on store).
3. In the three-dot menu, select **Repositories** and add:
   `https://github.com/blairbaldridge/TechCave-Govee2MQTT-Patch`
4. Install **Govee to MQTT Bridge (TechCave Tap-to-Run Patch)**. This compiles Rust on the host; the app has no prebuilt image. A Pi build may be slow and needs free disk space and memory.
5. Copy configuration from the original app to the new app's Configuration tab. Keep credentials private.
6. Stop the original app, turn off its Start on boot and Watchdog, then start the patched app. Never run both against the same broker.
7. Review the log and test DreamView Tap-to-Run scenes, normal lights and existing automations. Enable automatic startup only after testing.

See [detailed configuration and rollback](govee2mqtt_patch/DOCS.md).

## Source and validation

The Dockerfile fetches a fixed Git commit, checks the commit hash, uses Cargo's lockfile, copies upstream runtime assets/start script/license and checks the binary can start with `--help`. The Rust and Debian image tags are not digest-pinned. The MQTT implementation is unchanged by the PR. A fresh app has separate persistent storage.

This package has had static configuration checks only. No Docker build or live Govee/HA test has been performed. A compile failure, inaccessible commit or different API behavior is possible. Keep the original app available for rollback.

To try a build on an x86-64 Linux machine with Docker (without connecting to your production MQTT broker):

```bash
docker build --build-arg BUILD_FROM=ghcr.io/home-assistant/amd64-base-debian:bookworm -t techcave-govee2mqtt:0.1.0 govee2mqtt_patch
docker run --rm --entrypoint /app/govee techcave-govee2mqtt:0.1.0 --help
```

For real testing with Home Assistant Container, use an isolated broker and an appropriate standalone bridge image or a Supervisor/HA OS VM. Do not start this Supervisor app image as a normal Docker service; its startup uses Bashio/Supervisor services.

## Credits

- [Govee2MQTT](https://github.com/wez/govee2mqtt) by Wez Furlong, MIT license.
- Account-token fix by [UPEngineer](https://github.com/UPEngineer), PR #739. The author's [temporary add-on](https://github.com/UPEngineer/govee2mqtt-tap-to-run-fix) demonstrates the local-build approach.
- This repository packages the upstream PR as a separate TechCave app; it does not claim authorship of the upstream fix.

When the official Govee2MQTT release includes the fix, return to the official app. See LICENSE.md for the upstream MIT notice.
