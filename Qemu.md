# Running the CP19 image with QEMU (Linux / macOS)

This page shows a simple, repeatable workflow to run a CP19 image using QEMU. It covers converting a VMware `.vmdk` to `qcow2`, a recommended Linux (KVM) command, and a macOS (emulated) command. There are small optional tips for networking and troubleshooting.

## Prerequisites
- qemu-img and qemu-system-x86_64 installed
  - Debian/Ubuntu: `sudo apt install qemu qemu-kvm qemu-utils`
  - Fedora: `sudo dnf install @virtualization qemu-img`
  - macOS (Homebrew): `brew install qemu`
- unzip
- If on Linux and you want hardware acceleration: a kernel with KVM (and your user in the `kvm` group, or run as root).

## Quick steps
1. Create a working directory:
   ```sh
   mkdir cp19 && cd cp19
   ```
2. Copy the downloaded ZIP into the directory:
   ```sh
   cp "<PATH-TO/cp19*.zip>" .
   ```
3. Extract the ZIP:
   ```sh
   unzip cp19*.zip
   ```
4. Locate the correct `.vmdk` (the single descriptor/monolithic `.vmdk` — not the split `s001`, `s002`, ... parts). If the archive contains multiple `.vmdk` files, choose the descriptor (the one without `s00[1-9]` suffix).
5. Convert the `.vmdk` to `qcow2`:
   ```sh
   qemu-img convert \
     -f vmdk \
     -O qcow2 \
     "<PATH-TO-THE.vmdk>" \
     ./converted_image.qcow2
   ```
   Note: `-O` is a capital letter O (output format).

## Recommended QEMU command (Linux with KVM)
This is a general-purpose set of options that balances performance and usability:
```sh
qemu-system-x86_64 \
  -enable-kvm \
  -cpu host \
  -smp 4 \
  -m 4096 \
  -drive file=converted_image.qcow2,if=virtio,format=qcow2 \
  -netdev user,id=net0,hostfwd=tcp::2222-:22 \
  -device virtio-net-pci,netdev=net0 \
  -vga virtio \
  -display default
```
- `-enable-kvm` uses hardware acceleration (requires KVM).
- `-cpu host` passes host CPU features to the guest.
- `-netdev user,...hostfwd=tcp::2222-:22` forwards host port 2222 to guest port 22 (SSH). Adjust/remove if not needed.

Example to SSH into the guest from host:
```sh
ssh -p 2222 user@localhost
```

## QEMU command for macOS (Apple Silicon / Intel)
- On Intel macs with KVM-equivalent acceleration unavailable, performance will vary.
- On Apple Silicon (M1/M2) you will be emulating x86_64 — this is slower and uses TCG (`-accel tcg`). For native ARM guests use `qemu-system-aarch64` instead.

Example (x86_64 emulation on M* Mac devices):
```sh
qemu-system-x86_64 \
  -accel tcg \
  -cpu max \
  -smp 4 \
  -m 3072 \
  -drive file=converted_image.qcow2,if=virtio,format=qcow2 \
  -netdev user,id=net0,hostfwd=tcp::2222-:22 \
  -device virtio-net-pci,netdev=net0 \
  -vga std \
  -display default
```
- `-accel tcg` forces the interpreter/emulator (slower).
- Use `-vga std` for broader compatibility on macOS hosts.

## Tips and common tweaks
- Save the command as `run-qemu.sh` and make it executable:
  ```sh
  chmod +x run-qemu.sh
  ./run-qemu.sh
  ```
- If you need more CPU or RAM, increase `-smp` and `-m`.
- If the guest doesn't have virtio drivers (Windows guests), you may need to attach IDE or SATA instead of `if=virtio`, or install virtio drivers in the guest.
  Example SATA option:
  ```sh
  -drive file=converted_image.qcow2,if=none,format=qcow2,id=drive0 \
  -device ich9-ahci,id=ahci0 \
  -device ide-hd,drive=drive0,bus=ahci0.0
  ```
- For GUI-less operation or when running on a server, use `-nographic` (console) or `-display none` and connect via SSH or serial.
- To improve disk performance, consider `-drive cache=writeback` (beware of data-loss risks on power loss).

## Troubleshooting
- Permission / KVM errors: confirm KVM is available (`lsmod | grep kvm`) and you have permissions (or run as root).
- Black screen or no boot: check the chosen `.vmdk` is the correct disk (descriptor vs split parts). Try `qemu-img info converted_image.qcow2`.
- Networking not working: the simple `user` network is NATed; for bridge networking use `tap` or a bridge and appropriate host setup.

## Summary
- Convert the VM disk to `qcow2` with `qemu-img`.
- Prefer `-enable-kvm` and `-cpu host` on Linux for best performance.
- On macOS/Apple Silicon you will likely be using `-accel tcg` — expect slower performance.
- Use `hostfwd` to expose services (SSH) on the host for easy access to the guest.

If you want, I can:
- Add this improved file to the repository as a PR,
- Or modify it inline and push a commit — tell me which you prefer.
