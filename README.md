# Paprium H700 — PortMaster / muOS / KNULLI

Unofficial **integrated-emulation** adaptation of Paprium using a standalone SDL2 frontend and Genesis Plus GX PUNCHiUM. For Allwinner H700 ARM64 handhelds with 1 GB RAM. Maintained by **VVmImo**.

**Not a native game port, a RetroArch core or an official project. No game ROM or music is included.**

## Status — alpha 0.5.2

This is an **English documentation and installer-language revision** of 0.5.1. Audio processing and game behavior are unchanged.

| Firmware | Status | Download |
| --- | --- | --- |
| muOS | User-tested on RG40XX H with muOS 2601.0; audio improvement confirmed. Full-game testing remains incomplete. | `Paprium-H700-muOS-alpha-0.5.2.muxupd` |
| muOS manual extraction | Same engine; explicitly choose an installation card. | `paprium-h700-alpha-0.5.2.zip` |
| KNULLI | **Experimental: not hardware-tested on KNULLI.** | `Paprium-H700-KNULLI-alpha-0.5.2-EXPERIMENTAL.tar.gz` |

Get packages, manuals, complete sources and relink objects from [Releases](https://github.com/VVmImo/paprium-h700-portmaster/releases). No official PortMaster catalogue inclusion is implied. Older releases remain as history; use 0.5.2 for English documentation and installer messages.

## muOS installation

1. Install/update PortMaster for your firmware.
2. Copy the `.muxupd` into `ARCHIVE` and install with **Archive Manager**.
3. The destination is `/mnt/union/ROMS/Ports`, without requiring SD2. Find the physical installation on SD1 (`/mnt/mmc/ROMS/Ports`) or SD2 (`/mnt/sdcard/ROMS/Ports`). The archive's card does not select the unionfs write destination.
4. Supply the uncompressed ROM as `paprium/gamedata/paprium.bin` and all **52 named MP3 files** inside `paprium/gamedata/paprium/` in that same installation.
5. Open the **Paprium.sh file** in Ports and wait for automatic conversion.

To choose a card explicitly, extract the manual-install ZIP into its `ROMS/Ports`, leaving `Paprium.sh` beside `paprium`. Do not rename ZIP to `.muxupd` or duplicate the installation across both cards.

**[Detailed muOS guide](INSTALL-MUOS.txt)** — PC/SFTP paths, reference ROM, all track names, clean testing, controls and troubleshooting.

## KNULLI installation — experimental

1. Install/update PortMaster; see the [KNULLI documentation](https://knulli.org/systems/portmaster/).
2. Extract the TAR.GZ. Copy `Install Paprium.sh` and the complete `paprium-knulli` folder into `/userdata/roms/ports/` (`SHARE/roms/ports` over the network).
3. Update gamelists, run **Install Paprium** in Ports, wait for completion and update gamelists again.
4. Supply the ROM at `/userdata/roms/ports/paprium/gamedata/paprium.bin` and all MP3 files inside `/userdata/roms/ports/paprium/gamedata/paprium/`.
5. Open **Paprium** in Ports. Video, audio, controls, full conversion and menu return still require actual KNULLI hardware testing.

**[Detailed KNULLI guide](INSTALL-KNULLI.txt)**. The package does not modify `/usr/bin`, `custom.sh`, `knulli.conf` or the global EmulationStation catalogue.

## User-supplied game data

Reference ROM: **8,388,608 bytes**, product identifier `T-574120-00`, SHA-256:

```text
f9192ac55b915ac65765688bd4944c98d4ebe2ea994e6d312e2e6e52f7e79d09
```

Other revisions need testing. Renaming or editing a ROM header does not establish compatibility. Exact track names are in the guides and `docs/TRACKS.txt` in the source distribution.

The bundled converter generates PCM16 stereo **48 kHz WAV** from matching MP3 files only when valid WAV files are missing. It downloads no data, keeps MP3 files and reuses completed WAV files. Allow **at least 3 GB free after copying input data** for the reference soundtrack and maintain power during conversion. Invalid existing WAV files are preserved and cause an error.

Do not add game ROM, game MP3/WAV, BIOS, artwork or personal saves to public distributions. Audio under `tests/converter-fixtures` in the sources consists only of original synthetic test signals.

## Controls

| Physical input | Mega Drive / function |
| --- | --- |
| D-pad / left stick | Directions |
| Left / bottom / right / top face button | A / B / C / X |
| L1 / R1 | Y / Z |
| Start / Select | Start / Mode |
| **Select + Start** | Normal exit |
| **Select + R1** | Soft reset, used with the initial minigame |

Mapping uses SDL/PortMaster. Two controllers are supported in code but remain physically unverified.

## Saves, audio and limitations

SRAM: `paprium/saves/paprium.srm` (64 KiB). Changes are checked every five seconds and on normal exit. Only progress saved by the game is retained; there are no arbitrary-point savestates. Close the game before copying/restoring SRAM.

Audio retains the 0.5.1 per-voice amplification fix and **6 kHz filter for effects/echo only**. Music bypasses that effects filter. Output includes 5 Hz DC rejection, ROM PCM interpolation and about 50 ms of initial buffering. No WAV reconversion is needed. Original 4/8-bit effects still limit fidelity.

Comparison options: `PAPRIUM_SFX_FILTER=0` bypasses the effects filter; `PAPRIUM_LEGACY_AUDIO_FILTER=1` enables the old global filter, off by default. Cartridge emulation is experimental; exact reproduction and uninterrupted play throughout the game are not guaranteed.

Preserve `paprium/log.txt` and `paprium/conf/conversion-status.txt` before relaunching. Report model, firmware, package version, ROM hash and scene; do not send actual game files.

## Complete sources, build and relink

Complete corresponding sources: `paprium-h700-source-alpha-0.5.2.tar.gz` in this repository and Releases. Includes frontend, modified core, dependencies, scripts, tests and licenses. Every binary package includes `source.tar.gz` and `relink.tar.gz`; the relink kit is also available separately.

On Linux, extract the sources, enter `paprium-h700-source` and run:

```sh
bash scripts/setup-toolchain.sh
bash scripts/build.sh
```

Requires Python 3, CMake, curl and standard build tools. Setup downloads and verifies Bootlin ARM64 2020.08-1. SDL2/zlib sources are included. The device uses firmware SDL2, not the test-only SDL2 library. Target: ARMv8-A SIMD, Cortex-A53 tuning, GLIBC symbols up to 2.29.

For host tests, install QEMU user-mode, then run `bash scripts/build-test-sdl.sh`, `bash scripts/test.sh` and `python3 scripts/test-converter.py`. Synthetic tests do not replace firmware hardware validation.

For LGPL component replacement, follow `relink/README.txt` inside `relink.tar.gz`. Exact build objects and corresponding sources allow relinking without game data. Do not apply a blanket MIT/GPL label: components retain their own licenses.

## Credits and licenses

- [Genesis Plus GX](https://github.com/libretro/Genesis-Plus-GX): Charles MacDonald, EkeEke and contributors; includes non-commercial use/redistribution restrictions.
- [PUNCHiUM](https://github.com/pav1388/Genesis-Plus-GX-PUNCHiUM), branch `punchium`, commit `3849f3d3432df1d6320574e73695dd379ecef2b3`: experimental Paprium support base.
- [PAPwiiUM](https://github.com/Genesis64X/Genesis-Plus-G64X_PAPwiiUM): Wii adaptation reference, not the Linux executable used here.
- Nuked OPN2 (Nuke.YKT), blip_buf and NTSC filters (Shay Green): LGPL-2.1-or-later as stated in their files.
- SDL2, zlib, minimp3, dr_wav and stb_vorbis: individual authors/notices retained in the sources and THIRD-PARTY.txt.
- Game assets remain the property of their rights holders. This project grants no rights to those assets.

Read [LICENSE.txt](LICENSE.txt), THIRD-PARTY.txt and component notices. Non-commercial core terms do not replace specific LGPL/permissive rights. Modification for personal use and reverse engineering to debug modifications of LGPL-covered components are permitted under their terms. Complete library license texts are included.

**Unofficial and unaffiliated** with WaterMelon, Genesis Plus GX, PortMaster, muOS or KNULLI. GitHub publication is not a compatibility certification or authorization to redistribute game data.
