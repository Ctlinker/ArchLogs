# ArchLinux Third Install Logs

## Convention

**verionning**: X.X.X (major, minor, patch or fix) | BookMark - Desc

## 0.0.0 | Stable - Core Arch DX

Post Installed & Configured Arch Linux, Barbone Desktop Experience

---

### **Setup – Drive Repartition**

| Drive     | Label   | Size  | Mount-point | Subvol     | Filesystem | Purpose         |
| --------- | ------- | ----- | ----------- | ---------  | ---------- | --------------- |
| nvme0n1p1 |         | 2Gb   | /boot       |            | vfat       | boot partition  |
| nvme0n1p2 |         | 5Gb   | /.recover   | @recovery  | btrfs      | recovery system |
| nvme0n1p3 |         | 10Gb  | swap        |            | swap       | swap partition  |
| nvme0n1p4 | Root    | 70Gb  | /           | @          | btrfs      | root volume     |
| nvme0n1p4 | Root    |       | /var/log    | @log       | btrfs      | log sub volume  |
| nvme0n1p4 | Root    |       | /var/lib/pacman/pkg    | @pkg       | btrfs  | pkg vol  |
| nvme0n1p4 | Root    |       | /.snapshots | @snapshots | btrfs      | root volume     |
| nvme0n1p5 | Home    | 100Gb | /home       | @home      | btrfs      | user data       |
| nvme0n1p6 | AtlasOs | 100Gb | /atlas      | @atlas     | btrfs      | future WinApps  |

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
| bun     |     ✅    |        | JavaScript runtime, like Node but faster |
| deno    |     ✅    |        | Secure runtime for JS and TS             |
| node    |     ✅    |        | Node.js runtime                          |
| npm     |     ✅    |        | Node package manager                     |
| pnpm    |     ✅    |        | Fast Node package manager                |
| python  |     ✅    |        | Python interpreter                       |
| uv      |     ✅    |        | Low-level libuv utilities                |
| rust    |     ✅    |        | Rust programming language                |
| zig     |          |    ✅   | Zig language compiler                    |
| act     |          |    ✅   | GitHub Actions runner                    |
| moon    |     ✅    |        | Build system / task runner               |
| jq      |           |   ✅   | Command-line JSON processor              |
| go      |     ✅    |        | Go programming language                  |
| yarn    |     ✅    |        | Node package manager alternative         |

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

## 0.1.0 | Stable - Fonts Support

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
