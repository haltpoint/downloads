# Haltpoint: getting started

Haltpoint is a command-line tool for flashing, debugging and diagnosing ARM Cortex-M and RISC-V
microcontrollers through the debug probes you already have: SEGGER J-Link, ST-Link and CMSIS-DAP.
Its `doctor` command checks each step between your computer and the chip, and explains in plain
words what's wrong and how to fix it.

This guide is for **version 0.3.0**.

> **J-Link with SAM4E** is checked on our bench: doctor, erase, flash, RTT and `haltpoint test`
> all ran on an ATSAM4E16C with a J-Link. Please tell us what works and what doesn't on your
> boards. A copy of `haltpoint doctor` output is the most useful thing you can send.

## 1. Install

Download the file for your system from the list below. Each comes with a `.sha256` file, so you can check the
download with `shasum -a 256 -c FILE.sha256` (macOS) or `sha256sum -c FILE.sha256` (Linux).

| System | File | Install |
|---|---|---|
| macOS, Apple silicon | `haltpoint-v0.3.0-aarch64-apple-darwin.tar.gz` | Unpack it and put `haltpoint` on your `PATH`, e.g. `/usr/local/bin`. |
| macOS, Intel | `haltpoint-v0.3.0-x86_64-apple-darwin.tar.gz` | The same. |
| Debian, Ubuntu, Raspberry Pi OS | `haltpoint_0.2.0_amd64.deb`, `_arm64.deb` or `_armhf.deb` | `sudo apt install ./haltpoint_0.2.0_amd64.deb`. This also installs USB permission rules for common probes. |
| Other Linux | `haltpoint-v0.2.0-x86_64-unknown-linux-gnu.tar.gz` (also aarch64 and armv7) | Unpack it and put `haltpoint` on your `PATH`. Install the [probe-rs udev rules](https://probe.rs/docs/getting-started/probe-setup/) so you can open probes without root. |
| Windows | `haltpoint-v0.2.0-x86_64-pc-windows-msvc.zip` | Unpack it and put `haltpoint.exe` somewhere on your `PATH`. |

### Downloads

Version 0.3.0 is available for macOS. Linux and Windows stay on 0.2.0 for now. Their doctor
needs `--chip` to recognise a SAM4E and has no `--json`.

- [haltpoint-v0.3.0-aarch64-apple-darwin.tar.gz](https://github.com/haltpoint/downloads/raw/main/v0.3.0/haltpoint-v0.3.0-aarch64-apple-darwin.tar.gz) ([sha256](https://github.com/haltpoint/downloads/raw/main/v0.3.0/haltpoint-v0.3.0-aarch64-apple-darwin.tar.gz.sha256))
- [haltpoint-v0.3.0-x86_64-apple-darwin.tar.gz](https://github.com/haltpoint/downloads/raw/main/v0.3.0/haltpoint-v0.3.0-x86_64-apple-darwin.tar.gz) ([sha256](https://github.com/haltpoint/downloads/raw/main/v0.3.0/haltpoint-v0.3.0-x86_64-apple-darwin.tar.gz.sha256))
- [haltpoint_0.2.0_amd64.deb](https://github.com/haltpoint/downloads/raw/main/v0.2.0/haltpoint_0.2.0_amd64.deb) ([sha256](https://github.com/haltpoint/downloads/raw/main/v0.2.0/haltpoint_0.2.0_amd64.deb.sha256))
- [haltpoint_0.2.0_arm64.deb](https://github.com/haltpoint/downloads/raw/main/v0.2.0/haltpoint_0.2.0_arm64.deb) ([sha256](https://github.com/haltpoint/downloads/raw/main/v0.2.0/haltpoint_0.2.0_arm64.deb.sha256))
- [haltpoint_0.2.0_armhf.deb](https://github.com/haltpoint/downloads/raw/main/v0.2.0/haltpoint_0.2.0_armhf.deb) ([sha256](https://github.com/haltpoint/downloads/raw/main/v0.2.0/haltpoint_0.2.0_armhf.deb.sha256))
- [haltpoint-v0.2.0-x86_64-unknown-linux-gnu.tar.gz](https://github.com/haltpoint/downloads/raw/main/v0.2.0/haltpoint-v0.2.0-x86_64-unknown-linux-gnu.tar.gz) ([sha256](https://github.com/haltpoint/downloads/raw/main/v0.2.0/haltpoint-v0.2.0-x86_64-unknown-linux-gnu.tar.gz.sha256))
- [haltpoint-v0.2.0-aarch64-unknown-linux-gnu.tar.gz](https://github.com/haltpoint/downloads/raw/main/v0.2.0/haltpoint-v0.2.0-aarch64-unknown-linux-gnu.tar.gz) ([sha256](https://github.com/haltpoint/downloads/raw/main/v0.2.0/haltpoint-v0.2.0-aarch64-unknown-linux-gnu.tar.gz.sha256))
- [haltpoint-v0.2.0-armv7-unknown-linux-gnueabihf.tar.gz](https://github.com/haltpoint/downloads/raw/main/v0.2.0/haltpoint-v0.2.0-armv7-unknown-linux-gnueabihf.tar.gz) ([sha256](https://github.com/haltpoint/downloads/raw/main/v0.2.0/haltpoint-v0.2.0-armv7-unknown-linux-gnueabihf.tar.gz.sha256))
- [haltpoint-v0.2.0-x86_64-pc-windows-msvc.zip](https://github.com/haltpoint/downloads/raw/main/v0.2.0/haltpoint-v0.2.0-x86_64-pc-windows-msvc.zip) ([sha256](https://github.com/haltpoint/downloads/raw/main/v0.2.0/haltpoint-v0.2.0-x86_64-pc-windows-msvc.zip.sha256))

**macOS:** the binaries aren't signed yet. If macOS refuses to run `haltpoint`, clear the download
flag once with `xattr -d com.apple.quarantine /usr/local/bin/haltpoint`.

Check the install with `haltpoint --version`, which should print `haltpoint 0.3.0` on macOS
(`haltpoint 0.2.0` on Linux and Windows).

## 2. First connection: J-Link and SAM4E

1. Connect the J-Link to the board's SWD header and power the board. A SAM-ICE works too, since it
   is a J-Link for Microchip SAM parts.
2. Check that haltpoint sees the probe:

   ```
   haltpoint list
   ```

3. Run the connection check:

   ```
   haltpoint doctor
   ```

   On a SAM4E it reads the chip's ID and prints the exact part, for example
   `Chip identified: ATSAM4E16C (1 core)`. (With 0.2.0, name the chip yourself:
   `haltpoint doctor --chip ATSAM4E16C`. `haltpoint chips atsam4e` lists the four SAM4E names.)
   Doctor checks the probe, the target voltage, the SWD link at falling speeds, and the
   chip itself. Then it tells you what's wrong and how to fix it.

   If the chip's security bit is set, doctor reports that the chip is locked. The only way to
   unlock it is a full erase, which destroys the firmware on it, so haltpoint never erases unless
   you ask it to (see `haltpoint erase` below).

4. Save the chip name doctor printed. Every other command (`flash`, `erase`, `rtt`, `test`,
   ...) needs it, from `--chip` or from a `haltpoint.toml` at the root of your firmware project
   (haltpoint looks in the current folder and its parents):

   ```toml
   chip = "ATSAM4E16C"
   # Optional:
   # probe = "1366:0101"        # VID:PID[:SERIAL] when several probes are connected
   # speed_khz = 1000           # if doctor says the link needs a lower speed
   # connect_under_reset = true # if doctor says the chip only answers under reset
   ```

## 3. Everyday commands

| Command | What it does |
|---|---|
| `haltpoint flash app.elf` | Writes firmware, verifies it and restarts the chip. Takes ELF, Intel HEX, or a raw `.bin` with `--base 0x00400000` (the SAM4E flash start). |
| `haltpoint reset` | Resets the chip and lets it run. `--halt` stops it at the first instruction. |
| `haltpoint rtt --elf app.elf` | Shows what the firmware prints over SEGGER RTT, and sends lines you type back to it. `--log FILE`, `--grep TEXT` and `--timestamps` help with long sessions. |
| `haltpoint swo --clock-hz 120000000` | Shows ITM/SWO output. Pass your firmware's core clock. |
| `haltpoint gdb` | A GDB server on `localhost:1337` for `arm-none-eabi-gdb` or VS Code. |
| `haltpoint vscode build/app.elf` | Writes a VS Code (Cortex-Debug) setup, so F5 flashes and debugs. |
| `haltpoint erase --yes` | Erases the whole flash, destroying the firmware. |
| `haltpoint ui` | A page in your browser to list probes, run doctor, flash, erase, reset and watch RTT. |

Every command takes `--help`.

## 4. Using haltpoint from AI coding assistants and scripts

Haltpoint is a plain command-line tool with predictable exit codes, so an AI assistant (or a script,
or CI) can drive it the same way you do.

- **Exit codes.** Every command exits 0 on success and non-zero on failure. `haltpoint doctor`
  exits 1 if any check failed. `haltpoint test` exits 0 when the test passed, 1 when it failed,
  and 2 if neither result arrived in time.
- **Hardware tests in one command.** `haltpoint test` flashes test firmware, watches its RTT
  output for a pass or fail line, and exits with the result:

  ```
  haltpoint test build/tests.elf --pass "ALL TESTS PASSED" --fail FAIL --timeout 60 \
      --junit results.xml --log test.log
  ```

  `--junit` writes a JUnit XML report that CI systems display, and `--log` keeps the full output
  for the assistant to read afterwards.
- **Bounded runs.** `haltpoint rtt --seconds 10 --log out.log` stops by itself, so an assistant
  doesn't hang waiting on a terminal that never closes.
- **Diagnosis an assistant can act on.** `haltpoint doctor` prints each finding with its fix as
  plain text, which assistants read well. `haltpoint doctor --json` (0.2.1) prints the same findings as JSON.
- **Keep the board's settings in `haltpoint.toml`.** Then the assistant only runs
  `haltpoint flash`, `haltpoint test` and so on, without guessing chip names or probe IDs.
- **Destructive commands need an explicit flag.** `haltpoint erase` refuses to run without
  `--yes`. Review any assistant's request to add it.

## 5. When something goes wrong

Run `haltpoint doctor` first, and send us its full output along with:

- your system, and `haltpoint --version`,
- the probe (J-Link model, or SAM-ICE) and the board,
- the command you ran and what happened.

Release notes for 0.3.0: `haltpoint identify` reports what firmware is on a board and whether it
matches your files; a product manifest in haltpoint.toml (components, hardware ID, protected
regions for serials and calibration that survive flashing and erasing); `haltpoint test --uart`
for firmware that reports over a serial port; faster flashing over J-Link (a 1 MB SAM4E image
erases and flashes in about 9 s, on a par with SEGGER's JLinkExe on our bench); licence notices
in a `licenses` folder. Chips whose flash algorithm licences we haven't verified yet are left
out for now, among them STM32F1, SAM3, SAM4L, SAM4SD, EFM32/EFR32, Renesas RA and GD32;
`licenses/FLASH-ALGORITHMS.txt` has the full list. SAM4E is unaffected.

Release notes for 0.2.1: doctor identifies SAM4E parts by itself; `haltpoint doctor --json`;
shell completions and man pages in the Debian package; chips outside the built-in list through
`chip_description` in haltpoint.toml.

Release notes for 0.2.0: haltpoint ui; bench servers for sharing probes over the network
(`haltpoint serve`, with leases and per-user tokens); remote reset; Debian packages;
`haltpoint cert`; and a doctor finding for locked chips.
