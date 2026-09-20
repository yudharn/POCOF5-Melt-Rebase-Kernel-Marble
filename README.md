<div align="center">

![Kernel Melt Rebase](assets/social-preview.png)

# Kernel Melt Rebase

### Custom GKI Kernel for the Xiaomi POCO F5 / Redmi Note 12 Turbo

A rebase of **Melt Kernel** ([Pzqqt](https://github.com/Pzqqt)) for the **marble** platform, adding support for four different root managers plus a batch of modern kernel features: SuSFS, Re-Kernel, Baseband-guard, NoMount, and Droidspaces.

![Device](https://img.shields.io/badge/Device-marble-blue?style=flat-square)
![Kernel](https://img.shields.io/badge/Kernel-GKI_5.10-blue?style=flat-square)
![License](https://img.shields.io/badge/License-GPL--2.0-lightgrey?style=flat-square)

![KernelSU](https://img.shields.io/badge/KernelSU-supported-success?style=flat-square)
![KoWSU](https://img.shields.io/badge/KoWSU-supported-success?style=flat-square)
![KernelSU--Next](https://img.shields.io/badge/KernelSU--Next-supported-success?style=flat-square)
![ReSukiSU](https://img.shields.io/badge/ReSukiSU-supported-success?style=flat-square)
![SUSFS](https://img.shields.io/badge/SUSFS-integrated-success?style=flat-square)
![ROM](https://img.shields.io/badge/ROM-HyperOS%2FColorOS%2FOxygenOS-orange?style=flat-square)

[![Telegram](https://img.shields.io/badge/Telegram-meltrebase-26A5E4?style=flat-square&logo=telegram&logoColor=white)](https://t.me/meltrebase)

</div>

---

## Table of Contents

- [About This Project](#about-this-project)
- [Device Specifications](#device-specifications)
- [Key Features](#key-features)
- [Supported Root Managers](#supported-root-managers)
- [Build and Release Process](#build-and-release-process)
- [Installation](#installation)
- [Building from Source](#building-from-source)
- [Disclaimer](#disclaimer)
- [Credits and Acknowledgments](#credits-and-acknowledgments)
- [License](#license)

---

## About This Project

**Melt Rebase** is a custom kernel for marble (Xiaomi POCO F5 / Redmi Note 12 Turbo), built on top of the `melt-rebase` branch of **Melt Kernel** by [Pzqqt](https://github.com/Pzqqt), still the most widely used open-source kernel for this device.

Pzqqt intentionally keeps SUSFS integration and third-party root manager support out of the official source, so that's what this rebase fills in. On top of Melt Kernel you get four root manager variants to choose from, kernel-level root hiding through SUSFS, Baseband-guard locking down critical partitions, NoMount handling filesystem redirection, Droidspaces for running Linux containers, and Re-Kernel for freeze/thaw event reporting. Everything ships as ready-to-flash zips, built automatically.

---

## Device Specifications

| Item | Detail |
|---|---|
| Device | Xiaomi POCO F5 / Redmi Note 12 Turbo |
| Codename | `marble` |
| Chipset | Qualcomm Snapdragon 7+ Gen 2 (SM7475) |
| Kernel base | Linux 5.10.x, GKI 2.0 (`android12-5.10`) |
| Base source | [Pzqqt/android_kernel_xiaomi_marble](https://github.com/Pzqqt/android_kernel_xiaomi_marble) @ branch `melt-rebase` |
| Toolchain | LLVM/Clang |
| Packaging | AnyKernel3 |
| ROM compatibility | HyperOS, ColorOS and OxygenOS |

---

## Key Features

### SuSFS
The `susfs4ksu` patch from [simonpunk](https://gitlab.com/simonpunk) hides root traces at the kernel level, covering the `su` binary, mount points, and process names, so anything checking for root or system integrity comes up clean.

### Re-Kernel (Netlink & eBPF)
Reports background-process freeze/thaw events to userspace through two paths at once: a classic Netlink socket, and an eBPF daemon (`rekerneld`) that loads its own BPF program. Helps avoid the delayed or dropped notifications that aggressive freezers like MIUI/HyperOS tend to cause.

### Baseband-guard (BBG)
A lightweight LSM from [vc-teahouse](https://github.com/vc-teahouse) that blocks unauthorized writes to critical partitions like the baseband/modem and boot chain. Keeps a rogue module or process, even one running as root, from bricking your IMEI.

### NoMount
[maxsteeel](https://github.com/maxsteeel)'s VFS path-redirection framework. Instead of the usual overlay mount trick, it intercepts filesystem calls directly to inject or hide files, with a per-UID filter so banking apps and root scanners still see a clean filesystem.

### Droidspaces
Kernel-level container namespace support from [ravindu644](https://github.com/ravindu644), for running full Linux distros (systemd, OpenRC, etc.) directly on top of the Android kernel, each with its own process isolation, mount table, and cgroup hierarchy.

---

## Supported Root Managers

| Root Manager | Base | Notes |
|---|---|---|
| **KernelSU** (Official) | [tiann/KernelSU](https://github.com/tiann/KernelSU) | The original KernelSU implementation. |
| **KernelSU-KoWSU** | [deepongi-labs](https://github.com/deepongi-labs/KernelSU-KoWSU) | Personal fork focused on LKM support. |
| **KernelSU-Next** | [KernelSU-Next team](https://github.com/KernelSU-Next) | Fork with broader kernel compatibility and App Profile. |
| **ReSukiSU** | [ReSukiSU team](https://github.com/ReSukiSU) | SukiSU Ultra derivative, focused on stability and multi-manager support. |

All four get built in parallel on every release, so just grab the zip for whichever root manager you use from the **Releases** page.

> ⚠️ Don't install more than one root manager at a time on the same device.

---

## Build and Release Process

Builds run automatically through **GitHub Actions** across the whole root manager matrix, with periodic syncs from upstream Melt Kernel. Release notes also list the exact NoMount commit used for that build, so you always know which patch version you're flashing.

---

## Installation

1. Download the **Kernel** zip for your chosen root manager from the **Releases** page.
2. Back up your stock boot and vendor_boot images/partitions before doing anything.
3. Flash the zip through a custom recovery (TWRP/OrangeFox).
4. Reboot the device.
5. Install the matching manager app (KernelSU Manager, KernelSU-Next Manager, etc.) and finish the root setup.

---

## Building from Source

1. Clone this repo on the `melt-rebase` branch.
2. Set up the LLVM/Clang toolchain and a standard GKI kernel build environment.
3. Adjust the marble defconfig for whichever root manager you're targeting.
4. Build the kernel image, then package it with **AnyKernel3**.

Check the Actions tab or the workflow file itself if you want to see exactly how the official multi-manager builds get put together.

---

## ⚠️ Disclaimer

This is a custom kernel, **not official** from Xiaomi or Pzqqt. Install at your own risk, flashing a kernel always carries some chance of a bootloop. Always back up your stock boot and vendor_boot images before trying it.

Report issues related to SUSFS, NoMount, or any third-party root manager **in this repository**, not to upstream Melt Kernel or each feature's original project.

---

## Credits and Acknowledgments

This wouldn't exist without the work of:

**Kernel Source**
- [Pzqqt](https://github.com/Pzqqt): Melt Kernel author, upstream source and ongoing maintenance for marble
- [Xiaomi/MIUI kernel source maintainers](https://github.com/MiCode): base vendor kernel source

**Root Managers**
- [tiann](https://github.com/tiann): KernelSU
- [deepongi-labs](https://github.com/deepongi-labs): KernelSU-KoWSU
- [KernelSU-Next team](https://github.com/KernelSU-Next): KernelSU-Next
- [ReSukiSU team](https://github.com/ReSukiSU): ReSukiSU

**Kernel Features**
- [simonpunk](https://gitlab.com/simonpunk): `susfs4ksu` patch (SUSFS)
- [Sakion-Team](https://github.com/Sakion-Team): Re-Kernel (Netlink & eBPF)
- [vc-teahouse](https://github.com/vc-teahouse): Baseband-guard
- [maxsteeel](https://github.com/maxsteeel): NoMount
- [ravindu644](https://github.com/ravindu644): Droidspaces

**Tooling & Reference**
- [osm0sis](https://github.com/osm0sis): AnyKernel3
- [WildKernels](https://github.com/WildKernels): reference CI/CD and release patterns

---

## License

This project derives from the Linux kernel, so it follows the same **GPL-2.0** license as the upstream source and every component it uses.

<div align="center">

Built and maintained by **Yudharn**

</div>
