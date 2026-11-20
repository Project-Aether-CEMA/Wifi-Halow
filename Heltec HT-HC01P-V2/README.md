# Heltec HT-HC01P V2

Wi-Fi HaLow HAT for the Raspberry Pi, based on the Morse Micro **MM6108** chip, connected over **SPI**.

**Status:** ✅ Working: HaLow radio up and broadcasting on a single node.

| | |
|---|---|
| **Chip** | Morse Micro MM6108 |
| **Interface** | SPI (50 MHz) |
| **Host tested** | Raspberry Pi 4B |
| **Firmware** | OpenMANET 1.8.1 (OpenWrt 24.10), `rpi4-mm6108-spi` image |
| **Region** | EU (863–868 MHz) |
| **Tx power** | 16 dBm |

---

## Guides

Follow these in order:

1. **[RaspberryPi and HAT setup.md](<RaspberryPi and HAT setup.md>)**: flash OpenMANET, fit the Heltec pin layout and board config file, and check the HaLow radio starts.
2. **[WebUi Setup.md](<WebUi Setup.md>)**: run the OpenMANET setup wizard to create the HaLow mesh network.

---

## Files

| File | What it's for | SHA-256 |
|---|---|---|
| [`mm610x-spi-heltec.dtbo`](Files/mm610x-spi-heltec.dtbo) | Device tree overlay with the Heltec pin layout | `b6f9acf03caf1e4c3f1e6cea3d664ab7efd555bb6d7690d70011508240156d5c` |
| [`bcf_HC01P-V2.bin`](Files/bcf_HC01P-V2.bin) | Heltec's board config file for the V2 HAT (EU) | `cfff9880cae774b60c18cea88ed9d763b6c9d1e06f4a81fa8d1c2936d0a1c161` |
| `openmanet-1.8.1-rpi4-mm6108-spi-squashfs-sysupgrade.img.gz` | OpenMANET firmware for the Pi 4. **Not stored here:** download it from the [OpenMANET firmware page](https://openmanet.github.io/docs/firmware) | `abdbb37cb75457b82a0b02953cc24ffb0cbfa401d736e1747038a5c82ab8bfac` |

---

## Known issues

- **OpenMANET's Pi 4 image is built for the Seeed WM6108 HAT**, which uses different GPIO pins. Without the Heltec overlay, the radio keeps timing out. The setup guide fixes this.
- **EU channel must be `auto`.** A fixed channel stops the HaLow interface (`wlh0`) coming up.
- **Pi 4 Wi-Fi can't do upstream and hotspot at once.** On a mesh gate, use Ethernet or a travel router for the internet uplink, not the Pi's built-in Wi-Fi.
- **Re-flashing OpenMANET undoes the setup.** Repeat the setup guide afterwards.
