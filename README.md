# Wi-Fi HaLow

Tested setup guides for Wi-Fi HaLow (802.11ah) radios: HATs, modules and devices. Each one has its own folder with step-by-step guides and the files you need.

HaLow runs below 1 GHz (863–868 MHz in the EU, 902–928 MHz in the US), so it reaches much further than normal Wi-Fi at lower data rates. Most HaLow hardware uses a Morse Micro chip (MM6108 or MM8108), but each board wires it up differently, so a firmware image built for one board often won't work on another without changes.

---

## Devices

| Device | Chip | Interface | Host | Firmware | Region | Status |
|---|---|---|---|---|---|---|
| [Heltec HT-HC01P V2](<Heltec HT-HC01P-V2>) | MM6108 | SPI | Raspberry Pi 4B | OpenMANET 1.8.1 | EU | ✅ Working |

**Status key:** ✅ Working · 🟡 Partly working (see the device's notes) · 🔴 Not working yet · 🧪 Testing started

---

## How the repo is laid out

```
Wifi Halow/
├── README.md                  ← this page: the list of devices
├── _Template/                 ← copy this to start a new device
└── <Vendor> <Model>/          ← one folder per device or HAT
    ├── README.md              ← summary, status and links to the guides
    ├── <host> setup.md        ← getting the hardware working
    ├── WebUi Setup - <layout>.md ← setting up the HaLow network (one guide per layout)
    ├── Files/                 ← overlays, board config files, firmware
    └── images/                ← screenshots used by the guides
```

- **One folder per device or HAT**, named `<Vendor> <Model>` with the hardware revision if it matters (e.g. `Heltec HT-HC01P-V2`). V1 and V2 boards that need different files get separate folders.
- **One guide per host** if the same HAT is tested on more than one host (e.g. `RaspberryPi 4B setup.md` and `RaspberryPi 5 setup.md` in the same folder).
- **Each device folder has its own `README.md`.** GitHub shows it when someone opens the folder, so it should say what the device is, what was tested and which guide to start with.

---

## Adding a new device

1. Copy [`_Template`](_Template) and rename it `<Vendor> <Model>`.
2. Fill in its `README.md`: hardware, chip, interface, host, firmware version and region.
3. Write the guides, and put any files they need in `Files/`. Record each file's SHA-256 in the device README so people can check their download.
4. Add a row to the **Devices** table above, and keep its status up to date.

---

## Files and firmware

- **Small files** (device tree overlays `.dtbo`, board config files `.bin`) go in the device's `Files/` folder.
- **Firmware images are not stored in the repo.** Link to the official download and record the exact file name and SHA-256 in the device README. `.gitignore` keeps `.img` / `.img.gz` files out of commits, so you can keep a local copy in `Files/` safely.
- **Screenshots** go in the device's `images/` folder, named in the order they appear (`01-welcome-page.png`, `02-…`). Crop out browser bookmarks, tabs and anything personal before committing. Show them in a guide with `![What it shows](images/01-welcome-page.png)`.
- **Board config files are region-specific.** An EU board config file used on a US board, or the other way round, can transmit on the wrong frequencies. Always note the region.

---

## Shared tips

These apply to every OpenMANET / OpenWrt-based device:

- The first login at the default address has no password. Set one straight away.
- Use `scp -O` to copy files to OpenWrt devices; their SSH server doesn't support the newer copy method.
- After changing pins, overlays or board config files, **power off and unplug** rather than `reboot`, so the HaLow chip resets fully.
- Every node in a mesh needs the **same** country, channel, mesh ID and mesh password, and a **unique** hostname.
