# ArchLinux Third Install Logs

## Convention

**verionning**: X.X.X (major, minor, patch or fix) | BookMark -> Case[scope]: Desc
**scopes**:

- @apps
- @shell
- @kernel
- @boot
- @misc
- @graphic
- @gaming

**cases**:

- Feat : adding something
- Fix : fixing somthing
- ReImpl: refactoring somthing
- Patch: adding to a feature
- Revert: rollaback to older version

**bookmarks**:

- Experimental
- Stable
- Unstable

## 0.0.0 | Stable - Core Arch DX

Post Installed & Configured Arch Linux, Barbone Desktop Experience

---

### **Setup – Drive Repartition**

| Drive     | Label   | Size  | Mount-point         | Subvol     | Filesystem | Purpose         |
| --------- | ------- | ----- | ------------------- | ---------- | ---------- | --------------- |
| nvme0n1p1 |         | 2Gb   | /boot               |            | vfat       | boot partition  |
| nvme0n1p2 |         | 5Gb   | /.recover           | @recovery  | btrfs      | recovery system |
| nvme0n1p3 |         | 10Gb  | swap                |            | swap       | swap partition  |
| nvme0n1p4 | Root    | 70Gb  | /                   | @          | btrfs      | root volume     |
| nvme0n1p4 | Root    |       | /var/log            | @log       | btrfs      | log sub volume  |
| nvme0n1p4 | Root    |       | /var/lib/pacman/pkg | @pkg       | btrfs      | pkg vol         |
| nvme0n1p4 | Root    |       | /.snapshots         | @snapshots | btrfs      | root volume     |
| nvme0n1p5 | Home    | 100Gb | /home               | @home      | btrfs      | user data       |
| nvme0n1p6 | AtlasOs | 100Gb | /atlas              | @atlas     | btrfs      | future WinApps  |

> **Commands:**
>
> ```fish
> set P "discard=async,compress=zstd,ssd,relatime"
> mount -o "${P},subvol=<subvol>" /dev/<drive> /mnt/<mount-point> --mkdir
> mount -o  /dev/nvme0n1p1 /mnt/book --mkdir
> ```
>
> **NOTE:** For an obscure reason, `/etc/fstab` didn’t transfer correctly. Had to manually write/rewrite it.

---

### Setup - Pactrap

Base pkg installed thought Pactrap

> **Commands:**
>
> ```bash
> pactrap -K /mnt base linux-zen linux-zen-headers linux-firmware
> ```

---

### Setup - Base Os

> **Commands:**
>
> Pactrap base pkg overide to avoid future, colision on update
>
> ```bash
> pacman -S base linux-zen linux-zen-headers linux-firmware --overwrite="*"
> ```
>
> Base desktop experience
>
> ```bash
> pacman -S hyprland fish kitty \ # dx
>           sudo bash wget curl \ # utils
>           code zed nvim nano  \ # editors
>           libreoffice-fresh   \ # doc manager
>           iwd networkmanager  \ # network
>           less thunar wofi    \ # pager,file,app managers
>           grub btrfs-progs    \ # bootloader,filesys
>           hyprsunset brightnessctl
> systemctl enable NetworkManager
> ```

---

### Setup - ZSWAP

Edited `/etc/default/grub`, Added:

- zswap.enabled=1
- zswap.shrinker_enabled=1
- zswap.compressor=lz4
- zswap.max_pool_percent=30
- zswap.zpool=zsmalloc`

To `GRUB_CMDLINE_LINUX_DEFAULT`

---

### **Setup – Utilities / Apps (Post Install)**

> **Commands:**
>
> ```fish
> sudo pacman -S unzip gzip xz zip fastfetch man hyprpolkitagent network-manager-applet xdg-desktop-portal-hyprland cava qbittorrent yazi keepassxc
> ```
>
> **Notes:**
>
> - `unzip` → extract `.zip` archives
> - `gzip` → compress/decompress `.gz` files
> - `xz` → compress/decompress `.xz` files
> - `zip` → create `.zip` archives
> - `fastfetch` → lightweight system info tool for the terminal
> - `man` → manual pages for command-line utilities
> - `hyprpolkitagent` → PolicyKit agent for Hyprland (authentication dialogs)
> - `network-manager-applet` → GUI for NetworkManager to manage connections
> - `xdg-desktop-portal-hyprland` → portal backend for Flatpak apps under Hyprland
> - `cava` → terminal-based audio visualizer
> - `qbittorrent` → torrent client
> - `yazi` → lightweight music player
> - `keepassxc` → password manager

---

### **Setup – Git (Post Install)**

> **Commands:**
>
> Config: Barbone config
>
> ```fish
> sudo pacman -S git
> git config --global user.name "..."
> git config --global user.email "..."
> git config --global core.editor "..."
> ```
>
> SSH: Auth to git using ssh
>
> ```fish
> systemctl --user enable ssh-agent
> systemctl --user start ssh-agent
> ssh-keygen -b ... -t rsa -C ...
> ssh-agent -s  #! In fish `eval (ssh-agent -c)`
> ssh-add ~/.ssh/id_rsa
> cat ~/.ssh/id_rsa  # And set on GitHub
> ssh -T git@github.com  # log in GitHub
> ```

---

### Setup - USB / Removable Drive (Post Install)

> **Commands:**
>
> ```fish
> sudo pacman -S udisks2 ntfs-3g udiskie
> ```
>
> **Note**: Udiskie takes care of auto mount of usb, dunno why I need it now, I've never had to use it the previous install

---

### Setup - Thunar (Post Install)

> **Commands:**
>
> ```fish
> sudo pacman -S gvfs thunar-volman
> ```
>
> **Note**:
>
> - `gvfs` : trash management
> - `thunar-volman` : removable media behavior tunning (eg auto mount removable drive, exec cmd on event eg mouse detected)

---

### **Setup – Sound (Post Install)**

> **Commands:**
>
> ```fish
> sudo pacman -S pavucontrol pipewire wireplumber pipewire-jack pipewire-pulse pipewire-alsa pipewire-audio
> reboot
> ```
>
> **Notes:**
>
> - `pavucontrol` → GUI to manage audio levels and devices
> - `pipewire` → modern audio/video server (replacement for PulseAudio and JACK)
> - `wireplumber` → session manager for PipeWire, handles device routing
> - `pipewire-jack` → JACK API compatibility for pro audio apps
> - `pipewire-pulse` → PulseAudio replacement to allow legacy apps to work
> - `pipewire-alsa` → ALSA backend for PipeWire
> - `pipewire-audio` → core audio support for PipeWire

---

### **Setup – Fisher / Fish Plugin Manager (Post Install)**

Installed through fisher:

- **tide.fish**

- **fzf.fish**
  Dependencies:

  ```fish
  sudo pacman -S fzf fd bat
  ```

- **autopair.fish**

- **fish-ai.fish**
  Configuration file: `~/.config/fish-ai.ini`
  Dependencies and service setup:

  ```fish
  sudo pacman -S ollama
  systemctl enable --now ollama
  ```

- **fish_logo**

---

### **Setup – Proto / Language Manager (Post Install)**

Installed through **Proto**:

| Package | Built-in | Plugin | Description                              |
| ------- | :------: | :----: | ---------------------------------------- |
| bun     |    ✅    |        | JavaScript runtime, like Node but faster |
| deno    |    ✅    |        | Secure runtime for JS and TS             |
| node    |    ✅    |        | Node.js runtime                          |
| npm     |    ✅    |        | Node package manager                     |
| pnpm    |    ✅    |        | Fast Node package manager                |
| python  |    ✅    |        | Python interpreter                       |
| uv      |    ✅    |        | Low-level libuv utilities                |
| rust    |    ✅    |        | Rust programming language                |
| zig     |          |   ✅   | Zig language compiler                    |
| act     |          |   ✅   | GitHub Actions runner                    |
| moon    |    ✅    |        | Build system / task runner               |
| jq      |          |   ✅   | Command-line JSON processor              |
| go      |    ✅    |        | Go programming language                  |
| yarn    |    ✅    |        | Node package manager alternative         |

> **Commands:**
>
> ```fish
> proto install <pkg-name>
> ```

---

### **Setup – Extra Utilities**

> **Commands:**
>
> ```fish
> sudo pacman -S unrar btop htop
> ```
>
> **Notes:**
>
> - `unrar` → extract `.rar` archives
> - `btop` → resource monitor with CPU, memory, disk, network visualizations
> - `htop` → interactive process viewer for the terminal

## 0.1.0 | Stable -> Feat[@shell]: Fonts Support

Most/All Fonts Availables on Arch

> **Commands:**
>
> ```fish
>  sudo pacman -S  nerd-fonts \
>         adobe-source-code-pro-fonts \
>         adobe-source-sans-fonts \
>         adobe-source-serif-fonts \
>         cantarell-fonts \
>         terminus-font \
>         ttf-bitstream-vera \
>         ttf-dejavu \
>         ttf-droid \
>         ttf-fira-code \
>         ttf-fira-mono \
>         ttf-fira-sans \
>         ttf-font-awesome \
>         ttf-inconsolata \
>         ttf-liberation \
>         ttf-linux-libertine \
>         ttf-roboto \
>         ttf-ubuntu-font-family \
>         ttf-hack \
>         ttf-jetbrains-mono \
>         ttf-anonymous-pro \
>         ttf-carlito \
>         ttf-caladea \
>         ttf-freefont \
>         ttf-ibm-plex \
>         ttf-iosevka-nerd \
>         ttf-nerd-fonts-symbols \
>         gnu-free-fonts \
>         opendesktop-fonts \
>         terminus-font \
>         xorg-fonts-type1
> ```

## 0.2.0 | Stable -> Feat[@shell]: Yay Setup

Yay Aur Pkg Manager Set up

> **Commands:**
>
> Installation Process
>
> ```fish
> sudo pacman -S --needed git base-devel
> git clone https://aur.archlinux.org/yay-bin.git
> cd yay-bin
> makepkg -si
> ```
>
> Sync with db
>
> ```fish
> yay -Y --gendb
> yay -Syu --devel
> yay -Y --devel --save
> ```

## 0.3.0 | Unstable - Feat[@shell] Snapper Setup

### **Setup – Basic Snapshots Support**

> **Commands:**
>
> Installation Process
>
> ```fish
> sudo pacman -S snapper
> ```
>
> **Notes:**
>
> - `snapper` → create, compare, revert between btrfs snapshots
>
> Snapshots setup
>
> **Note: since i've already had a .snapshot folder had to rm it**
>
> Fix Command
>
> ```fish
> sudo unmount /.snapshots
> sudo rm -rf /.snapshots
> ```
>
> Normal command
>
> ```fish
> sudo snapper -c root create-config /
> ```
>
> Rest of fix
>
> ```fish
> sudo rm -rf /.snapshots
> sudo mount -o subvol=@snapshots /dev/nvme0n1p4 /.snapshots --mkdir
> ```

### **Unstability Reason**

Non configured, auto-snapshot limit. From seconde install, This was a real problem,
As snapper keep reference, suppressed files & uncompressed version of files after,
`btrfs filesystem defragment` and `btrfs filesystem balance`

## 0.3.1 | Stable - Fix[@shell]: Locale error with `"C"` (missing UTF-8)

Fixed minor error with system fonts

> **Commands:**
>
> ```fish
> sudo localedef -i en_US -f UTF-8 en_US.UTF-8
>
> set -U LANG en_US.UTF-8
> set -U LC_ALL en_US.UTF-8
> ```

## 0.4.0 | Stable -> Feat[@shell] Quickshell Barbone support

> **Commands:**
>
> Installation Process
>
> ```fish
> yay -S quickshell
> sudo pacman -S qt6-tools
> ```

---

## 0.5.0 | Stable -> Feat[@apps]: Installed Android Studio

Basic Android studio setup

> **Commands:**
>
> ```fish
> # Download the Android SDK.tar & Extract it
>
> cd android-studio &&
> chmod +x ./bin/studio.sh
>
> set -Ux ANDROID_HOME /home/cat/Android/Sdk/ #check the path match
> ./bin/studio.sh
> ```

---

## 0.5.1 | Stable -> Fix[@shell]: Fix Missused Universal vars

> **Commands:**
>
> ```fish
> set -Ux PAGER less
> set -Ux EDITOR nvim
> set -Ux LANG en_US.UTF-8
> set -Ux LC_ALL en_US.UTF-8
> ```

---

## 1.0.0 | Stable -> Fix[@graphics]: Intel Graphics

Intel GPU Driver & Wayland Environment Setup

> **Description:**
>
> Configures Intel GPU acceleration (Mesa, Vulkan, VA-API), Wayland display system, and desktop utilities for Hyprland environment.
>
> **Observation:**
>
> My PC fly like a rocket, Android Studio, VM, Heavy Game, It doesn't flinch anymore, I have to stick my head to the fan ouput to be able to hear it, even at night

---

### **Setup — Drivers & Utils**

> **Commands:**
>
> ```fish
> sudo pacman -S --needed \
>     mesa \
>     vulkan-intel \
>     libva-intel-driver \
>     intel-media-driver \
>     vulkan-tools \
>     libvdpau-va-gl \
>     intel-gpu-tools \
>     mesa-utils \
>     libva-utils \
>     linux-firmware # Required for newer Intel GPUs (e.g. Skylake)
> ```
>
> **Note:**
>
> | Package                | Description                                                                           |
> | ---------------------- | ------------------------------------------------------------------------------------- |
> | **mesa**               | Core OpenGL and Vulkan implementation for hardware-accelerated rendering.             |
> | **vulkan-intel**       | Intel’s Vulkan driver for modern GPU rendering and 3D applications.                   |
> | **libva-intel-driver** | Legacy VA-API driver for hardware-accelerated video decoding (older Intel GPUs).      |
> | **intel-media-driver** | Modern VA-API driver supporting Broadwell and newer Intel GPUs.                       |
> | **vulkan-tools**       | Tools such as `vulkaninfo` for inspecting Vulkan device support and features.         |
> | **libvdpau-va-gl**     | Compatibility layer translating VDPAU calls into VA-API (for apps expecting VDPAU).   |
> | **intel-gpu-tools**    | Debugging and monitoring utilities for Intel GPUs (`intel_gpu_top`, etc.).            |
> | **mesa-utils**         | Basic OpenGL tools like `glxinfo` and `glxgears` for testing 3D acceleration.         |
> | **libva-utils**        | Utilities to test and debug VA-API support (`vainfo`, `vdpauinfo`).                   |
> | **linux-firmware**     | Essential binary firmware files used by modern Intel GPUs and other hardware devices. |

---

### **Setup — Wayland**

> **Commands:**
>
> ```fish
> sudo pacman -S --needed \
>     wayland \
>     wayland-protocols \
>     xorg-xwayland
> ```
>
> **Note:**
>
> | Package               | Description                                                                                                                                                      |
> | --------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
> | **wayland**           | Core Wayland display protocol libraries and utilities used by compositors and clients.                                                                           |
> | **wayland-protocols** | Official collection of additional Wayland protocol extensions (e.g., screen capture, idle inhibit, layer-shell).                                                 |
> | **xorg-xwayland**     | Compatibility layer providing an embedded X11 server inside Wayland, allowing legacy X applications to run seamlessly under Wayland compositors (like Hyprland). |

---

### **Setup — Desktop Shell**

> **Commands:**
>
> ```fish
> sudo pacman -S --needed \
>     gtk3 gtk4 \
>     qt5-wayland qt6-wayland
> ```
>
> **Note:**
>
> | Package         | Description                                                                             |
> | --------------- | --------------------------------------------------------------------------------------- |
> | **gtk3**        | GTK 3 toolkit for building graphical applications (used by many Linux apps).            |
> | **gtk4**        | GTK 4 toolkit, latest version with Wayland support and improved rendering.              |
> | **qt5-wayland** | Qt 5 platform plugin for Wayland, enabling Qt5 applications to run natively on Wayland. |
> | **qt6-wayland** | Qt 6 platform plugin for Wayland, enabling Qt6 applications to run natively on Wayland. |

---

### **Setup — Desktop Utils**

> **Commands:**
>
> ```fish
> sudo pacman -S --needed \
>     xdg-desktop-portal-hyprland \
>     xdg-desktop-portal \
>     xdg-utils \
>     wl-clipboard \
>     grim slurp \
>     waybar \
>     swaync
>
> yay -S ironbar
> ```
>
> **Note:**
>
> | Package                         | Description                                                                                                          |
> | ------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
> | **xdg-desktop-portal-hyprland** | Implementation of XDG portal interfaces for Hyprland, enabling screenshots, file pickers, and other portal features. |
> | **xdg-desktop-portal**          | Generic XDG desktop portal service for sandboxed applications (Flatpak, Snap) to interact with the desktop.          |
> | **xdg-utils**                   | Command-line utilities for desktop integration (open URLs, mime-types, default apps).                                |
> | **wl-clipboard**                | Clipboard utilities for Wayland (copy/paste between apps).                                                           |
> | **grim**                        | Wayland screenshot utility.                                                                                          |
> | **slurp**                       | Tool to select a screen region under Wayland (often used with `grim`).                                               |
> | **waybar**                      | Highly configurable status bar for Wayland compositors like Hyprland.                                                |
> | **swaync**                      | Notification daemon for Wayland (similar to `dunst` on X11).                                                         |
> | **ironbar (AUR)**               | Custom Hyprland bar available via AUR (`yay`) for additional status/info display.                                    |

---

### **Implementation Reference**

Graphics on Linux operate across several layers: **kernel**, **drivers**, **libraries**, and **display/compositor**.

- **Kernel level:** `i915` (Intel GPU kernel module; alternatives include `xe`, etc.)  
  Handles direct communication with the GPU, mode setting (KMS), and low-level hardware management.

- **Driver / microcode layer:** e.g., `intel-ucode`  
  Provides CPU/microcode updates necessary for stable GPU operation, enabling the kernel module to talk to the hardware safely.

- **Libraries / APIs:** e.g., OpenGL (Mesa), Vulkan (`vulkan-intel`)  
  Provide user-space interfaces for complex GPU operations, including 3D rendering and general GPU computation.

- **Display server / compositor:** e.g., `xorg-server`, `xorg-xwayland`  
  Provides windowing, input/output, and graphical context, connecting applications to the underlying GPU APIs.

> **Hardware acceleration:** Offloading work from the CPU to specialized hardware.  
> This includes:
>
> - GPU rendering (aka GPU acceleration)
> - Video decode/encode engines (Intel QuickSync, AMD VCE/VCN, NVIDIA NVDEC/NVENC)
> - Audio DSPs (less common)
> - Cryptographic accelerators (AES-NI, etc.)

**Example in your context:**

- `mesa + vulkan-intel` → GPU acceleration (3D/OpenGL/Vulkan rendering)
- `libva-intel-driver` / `intel-media-driver` → Hardware acceleration for video decoding (may use GPU video engine without touching shaders)

> In practice, “Intel GPU hardware acceleration” usually refers to **both graphics (3D) and video decoding offloaded to the GPU**.

### **Checkup — Validation**

> **Commands:**
>
> ```fish
> # Monitor Intel GPU activity
> sudo intel_gpu_top
> # (Video engine usage > 0% when playing video)
>
> # Confirm GPU driver
> lspci -k | grep -A3 -E "VGA|3D"
>
> # Check VA-API / video acceleration
> vainfo
>
> # Renderer info (OpenGL)
> glxinfo | grep "OpenGL renderer"
>
> # Renderer info (Vulkan)
> vulkaninfo | grep "deviceName"
>
> # Confirm Wayland session
> echo $XDG_SESSION_TYPE
> ```

---

## 1.1.0 | Stable -> Feat[@apps]: Semi Windows Apps Support (Wine)

> **Commands:**
>
> ```fish
> sudo pacman -Syu wine wine-gecko wine-mono winetricks zenity
> ```

Note: edited `/etc/pacman.conf` uncommented out `multilib` entry

## 1.2.0 | Stable -> Feat[@apps]: Xamp PHP Dev Server

> **Commands:**
>
> ```fish
> pacman -S xampp
> ```

## 1.3.0 | Stable -> Feat[@shell]: Speech Synthesis

> **Desciption:**
>
> Fully configured `llm` based speech to text synthesis through `piper`

### Setup - Speech Dispatcher

> **Commands:**
>
> ```fish
> sudo pacman -S speech-dispatcher
> spd-conf
> ```

### Setup - Piper & Piper Voices

> **Commands:**
>
> Get `piper` run:
>
> ```fish
> yay -S piper-tts-bin piper-voices-en-us
> ```
>
> To Configure `piper` and `speech-dispatcher`
>
> Add the line `
> AddModule "piper-tts-generic" "sd_generic" "piper-tts-generic.conf"
> ` to `~/.config/speech-dispatcher/speechd.conf`
>
> And set `DefaultModule    "piper-tts-generic"`
