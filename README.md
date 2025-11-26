## 64-Bit Kernel
```markdown


A simple 64-bit operating system kernel written in C and x86_64 Assembly. This project demonstrates the basics of booting a 64-bit kernel using the Multiboot2 standard, setting up paging and the Global Descriptor Table (GDT), and printing text directly to the screen using VGA memory.  

---

## Features

- Boots in **64-bit long mode** using a custom bootloader.
- Multiboot2 compliant for compatibility with GRUB.
- Minimal VGA output: prints `"I can boot a kernel so I AM COOL!"`.
- Demonstrates:
  - Page table setup
  - Long mode enablement
  - GDT configuration
  - Simple kernel printing API
- Fully cross-compiled in a Docker environment.

---

## Project Structure

64-Bit-Kernel/
│
├─ buildenv/
│ └─ Dockerfile # Docker image setup with cross-compiler and build tools
│
├─ src/
│ ├─ impl/
│ │ ├─ kernel/
│ │ │ └─ main.c # Main kernel entry point
│ │ ├─ x86_64/
│ │ │ ├─ boot/
│ │ │ │ ├─ header.asm # Multiboot header
│ │ │ │ ├─ main.asm # 32-bit bootloader
│ │ │ │ └─ main64.asm # 64-bit long mode entry
│ │ │ └─ print.c # Kernel printing implementation
│ └─ intf/
│ └─ print.h # Header for printing API
│
├─ targets/
│ └─ x86_64/
│ ├─ linker.ld # Linker script
│ └─ iso/
│ └─ boot/
│ └─ grub/
│ └─ grub.cfg # GRUB configuration
│
├─ Makefile # Build rules for kernel and ISO
├─ .gitignore # Ignore build outputs and temporary files
└─ README.md # This file

---

## Prerequisites

To build and run this kernel, you need:

1. **Docker**  
   - Linux: `sudo apt install docker.io`  
   - Windows: Docker Desktop with WSL2 backend

2. **QEMU** for emulation:  
   ```bash
   sudo apt install qemu-system-x86
````

3. (Optional) **Git** for cloning:

   ```bash
   sudo apt install git
   ```

---

## Build & Run Instructions

1. **Clone the repository**

```bash
git clone https://github.com/Anjas2005/64-Bit-Kernel-.git
cd 64-Bit-Kernel-
```

2. **Build Docker image**

```bash
docker build -t myos-buildenv buildenv/
```

3. **Run Docker container and build kernel**

```bash
docker run --rm -it -v ${PWD}:/root/env myos-buildenv
```

Inside the Docker container:

```bash
make build-x86_64
exit
```

4. **Run the kernel ISO in QEMU**

```bash
qemu-system-x86_64 -cdrom dist/x86_64/kernel.iso
```

**Notes:**

* On Windows, replace `${PWD}` with the full path, e.g., `C:/path/to/64-Bit-Kernel`.
* The build produces `dist/x86_64/kernel.iso`, which can also be written to a USB for testing on real hardware.

---

## Build System

* **Makefile** automates:

  * Compiling kernel C code and x86_64 assembly
  * Linking with a custom linker script
  * Generating the ISO with GRUB
* **Dockerfile** ensures consistent environment with:

  * `x86_64-elf-gcc`
  * `nasm`
  * `grub-mkrescue`
  * `xorriso`

---

## Kernel Printing API

* **print_clear()**: Clears the screen.
* **print_char(char)**: Prints a single character.
* **print_str(char*)**: Prints a string.
* **print_set_color(foreground, background)**: Sets text color.

---

## How It Works

1. **Bootloader (header.asm + main.asm)**:

   * Multiboot2 compliant header
   * Checks CPU features
   * Enables paging
   * Jumps to 64-bit long mode

2. **Long mode entry (main64.asm)**:

   * Sets data segments to null
   * Calls `kernel_main()` in C

3. **Kernel (main.c + print.c)**:

   * Clears the screen
   * Prints the message with specified color

4. **ISO creation**:

   * GRUB bootloader configured via `grub.cfg`
   * `grub-mkrescue` packages kernel into bootable ISO

---

## Notes & Tips

* Tested on QEMU 7.x and Linux.
* The project is minimal and educational: it’s a foundation for building more complex OS features.
* Make sure your terminal supports running Docker with volume mounts (`-v ${PWD}:/root/env`).

---
