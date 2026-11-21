# Heltec HT-HC01P V2

Wi-Fi HaLow HAT for the Raspberry Pi, based on the Morse Micro **MM6108** chip, connected over **SPI**.

**Status:** ✅ Working in both layouts with two nodes: mesh (needs the [EU/UK mesh fix](<WebUi Setup - Mesh.md#2-fix-the-euuk-mesh-block>)) and star (access point + clients). In the EU, devices on the hotspots fill the HaLow link; traffic control is still being worked on.

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

1. **[RaspberryPi and HAT setup.md](<RaspberryPi and HAT setup.md>)**: flash OpenMANET, fit the Heltec pin layout and board config file, and check the HaLow radio starts. Same for both layouts.
2. Choose a layout ([comparison below](#choosing-a-layout)), then follow **one** of:
   - **[WebUi Setup - Mesh.md](<WebUi Setup - Mesh.md>)**: nodes relay for each other
   - **[WebUi Setup - Star.md](<WebUi Setup - Star.md>)**: every node talks directly to the base

---

## Choosing a layout

**Mesh:** nodes relay for each other, so someone out of the base's range can reach it through a node in between.

```
 Internet ── Ethernet ── BASE (Mesh Gate)
                           ╎ HaLow
              ┌────────────┴────────────┐
           Pi (A) ─────── Pi (B) ─────── Pi (C)    ← relay for each other
             │              │              │
          Phone A        Phone B        Phone C
```

**Star:** every carried node connects straight to the base, like phones to a Wi-Fi router.

```
 Internet ── Ethernet ── BASE (HaLow access point)
                           ╎ HaLow
              ┌────────────┼────────────┐
           Pi (A)        Pi (B)        Pi (C)      ← each must reach the base
             │              │              │
          Phone A        Phone B        Phone C
```

### Pros and cons

| | **Mesh** | **Star** |
|---|---|---|
| **Range** | ✅ Extends with every node. People in the middle relay for people further out. | ❌ Limited to the base's reach. Anyone out of range of the base is cut off, even if they're next to another node. |
| **If a node fails or moves** | ✅ The mesh re-routes through other nodes automatically | ✅ Only that node drops; the rest aren't affected |
| **If the base fails** | ⚠️ Internet and TAK Server are lost, but nodes can still reach each other | ❌ Everything stops; carried nodes can't reach each other either |
| **Speed in the EU/UK** | ❌ Very slow. Each node gets about 2.8% airtime, and the mesh's own management traffic uses some of it. | ⚠️ Should be a little better: the access point gets about 10% airtime and there's no mesh overhead. But each carried node is still a client limited to about 2.8%. |
| **Supported in the EU/UK** | ❌ Blocked by OpenMANET's driver script. Needs a one-line change that a reflash undoes. Unsupported by Morse Micro. | ✅ Works as shipped. Recommended by Morse Micro for the EU. |
| **Channel** | ⚠️ Fixed channel on every node, which can't move away from interference | ✅ Automatic. The base picks the channel and moves if needed, and the clients follow. |
| **Setup** | ✅ The wizard does most of it, plus the EU/UK fix | ⚠️ The wizard, then a few commands on each node to switch modes |
| **Base antenna** | Helpful | **Essential.** Mount it as high as possible: mast, roof or vehicle. |

The airtime figures are from the [Morse Micro forum](https://community.morsemicro.com/t/understanding-eu868-restrictions/1341): about 2.8% for a client or mesh node and about 10% for an access point under the EU duty cycle.

**Which one?**
- **Everyone stays within about 1 km of a high base antenna:** choose **star**. It needs no changes to OpenMANET.
- **People spread out beyond the base's reach, or ground and buildings block the base:** choose **mesh**, and accept the slower speeds.

**Either way, in the EU/UK you must control what crosses HaLow.** One phone or laptop left to use it freely fills the link for everyone. Both guides cover this.

### Test results

Two nodes a few metres apart (signal -10 dBm), 2 MHz channel, EU duty cycle on. Ping from the base station:

| Test | Mesh | Star |
|---|---|---|
| Link rate | 7.2–7.5 Mbit/s | Not recorded |
| Base → carried node, **nothing on the hotspot** | **0.25–1 s**, occasionally 10–30 ms, no loss | Not yet tested |
| Base → carried node, devices on the hotspot | Not tested | 9–16 s, 68% loss (phone on the hotspot) |
| Base → device on the carried node's hotspot | 11–24 s, 60–80% loss (iPhone) | 6–11 s, 55% loss (Mac) |

- **No fair comparison yet.** The only test with nothing on the hotspot was the mesh one. The results with devices connected show the link full of their own background traffic, not what each layout can do. The star result with a device connected used a Mac and the mesh one an iPhone, so those aren't a fair comparison either.
- **The link rate isn't the limit.** Both layouts link at about 7.2 Mbit/s. The duty-cycle limit leaves roughly **200 kbit/s at best** per carried node, and a phone or laptop syncing in the background sends more than that.
- **To do:** star with nothing on the hotspot, and a measurement of how much a phone sends.

---

## Files

| File | What it's for | SHA-256 |
|---|---|---|
| [`mm610x-spi-heltec.dtbo`](Files/mm610x-spi-heltec.dtbo) | Device tree overlay with the Heltec pin layout | `b6f9acf03caf1e4c3f1e6cea3d664ab7efd555bb6d7690d70011508240156d5c` |
| [`bcf_HC01P-V2.bin`](Files/bcf_HC01P-V2.bin) | Heltec's board config file for the V2 HAT (EU) | `cfff9880cae774b60c18cea88ed9d763b6c9d1e06f4a81fa8d1c2936d0a1c161` |
| `openmanet-1.8.1-rpi4-mm6108-spi-squashfs-sysupgrade.img.gz` | OpenMANET firmware for the Pi 4. **Not stored here:** download it from the [OpenMANET firmware page](https://openmanet.github.io/docs/firmware) | `abdbb37cb75457b82a0b02953cc24ffb0cbfa401d736e1747038a5c82ab8bfac` |

---

## Known issues

- **OpenMANET's Pi 4 image is built for the Seeed WM6108 HAT**, which uses different GPIO pins. Without the Heltec overlay, the radio keeps timing out. The hardware guide fixes this.
- **EU/UK: mesh is blocked by default.** OpenMANET's driver script refuses mesh mode for country EU/GB, so nodes never connect. The [mesh guide](<WebUi Setup - Mesh.md#2-fix-the-euuk-mesh-block>) fixes it with a one-line script change and a fixed channel.
- **EU channel:** an access point needs channel `auto`; a mesh needs a fixed channel (`2` at 2 MHz).
- **EU/UK is slow in both layouts.** About 200 kbit/s at best per carried node. A phone's background traffic can push delays to 10–20 s, so keep phones in Low Data Mode.
- **Pi 4 Wi-Fi can't do upstream and hotspot at once.** On the base, use Ethernet or a travel router for the internet uplink, not the Pi's built-in Wi-Fi.
- **The wizard only offers mesh roles.** The star guide runs it, then switches each node with a few commands.
- **Re-flashing OpenMANET undoes the setup.** Repeat the hardware guide and your layout's guide afterwards.
