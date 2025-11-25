# PolyLinux Game - Complete Setup Guide

> A Polymorphic Linux Security Training Lab
> Originally developed at Penn State University for Cybersecurity Education

---

## Table of Contents

1. [What Is This? (Layman's Terms)](#what-is-this-laymans-terms)
2. [What Is Buildroot Linux?](#what-is-buildroot-linux)
3. [How Does This Project Use Buildroot?](#how-does-this-project-use-buildroot)
4. [Technical Overview](#technical-overview)
5. [Setup Options](#setup-options)
6. [Option A: Raspberry Pi with Buildroot (Advanced)](#option-a-raspberry-pi-with-buildroot-advanced)
7. [Option B: Raspberry Pi OS / Debian (Easier)](#option-b-raspberry-pi-os--debian-easier)
8. [Option C: Virtual Machine](#option-c-virtual-machine)
9. [Playing the Game](#playing-the-game)
10. [Available Challenge Sets](#available-challenge-sets)
11. [Troubleshooting](#troubleshooting)

---

## What Is This? (Layman's Terms)

**PolyLinux Game** is an interactive security training game that teaches you Linux command-line skills through hands-on challenges. Think of it like a video game, but instead of pressing buttons, you type Linux commands to solve puzzles.

### The "Polymorphic" Part

Here's what makes this special: **every user gets different puzzles**.

When you set up the game, you enter an email address (or any text - it doesn't actually verify anything). The system creates a unique "fingerprint" (called a hash) from what you typed, and uses that to generate YOUR specific set of challenges.

**Why does this matter?**
- In a classroom setting, students can't copy each other's answers
- Every time you reinstall, you can get a fresh set of challenges
- It's great for repeat practice

### What You'll Learn

- Navigating the Linux file system (`cd`, `ls`, `pwd`)
- Finding files (`find`, `grep`, `locate`)
- Reading and manipulating files (`cat`, `head`, `tail`, `strings`)
- Understanding file permissions
- Working with hidden files (files starting with `.`)
- Decoding encoded data (Base64, hexadecimal)
- Basic security concepts

---

## What Is Buildroot Linux?

Imagine you're building a house. You could buy a pre-built mansion with 50 rooms, a pool, a bowling alley, and a home theater - but you only need a small cabin to sleep in.

**Regular Linux distributions** (like Ubuntu, Fedora, or Raspberry Pi OS) are like that mansion. They come with:
- Web browsers
- Office applications
- Media players
- Thousands of utilities you'll never use
- Gigabytes of stuff

**Buildroot** is a tool that lets you build your own tiny, custom Linux from scratch - just the essentials you actually need. The result is:
- Incredibly small (can be under 50MB)
- Boots in seconds
- Perfect for embedded devices (routers, smart appliances, industrial equipment)
- No "extra stuff" to get in the way

### Key Differences

| Feature | Regular Linux (Ubuntu/Raspberry Pi OS) | Buildroot Linux |
|---------|----------------------------------------|-----------------|
| Size | 2-8 GB | 50-200 MB |
| Boot time | 30-90 seconds | 5-15 seconds |
| Shell | Bash (full-featured) | Ash/BusyBox (minimal) |
| Package manager | apt, dnf, etc. | None (you build what you need) |
| Home directory | `/home/username` exists | Often doesn't exist by default |
| Target use | Desktop/Server | Embedded devices |

---

## How Does This Project Use Buildroot?

This project was designed to **run ON TOP OF** Buildroot Linux. It doesn't configure Buildroot itself - rather, it expects to be installed on an already-running Buildroot system.

### Why Buildroot Was Chosen

1. **Controlled Environment**: Students can't "cheat" by using tools that aren't installed
2. **Minimal Distractions**: No GUI, no extra applications - just the command line
3. **Teaches Real Skills**: Many embedded systems and servers use minimal Linux environments
4. **Lightweight**: Runs great on low-powered hardware like Raspberry Pi
5. **Reproducible**: Every student gets the exact same starting environment

### Buildroot-Specific Accommodations in the Code

The scripts include special handling for Buildroot:

```sh
## buildroot doesn't have a home directory by default, so we add one
mkdir /home/
```

```sh
# (wrong syntax for Buildroot/ash)
# Uses /bin/sh instead of /bin/bash throughout
```

---

## Technical Overview

### Project Structure

```
polybandit3/
├── README.md                 # Original minimal readme
├── SETUP_GUIDE.md           # This file
│
├── INSTALLERS
│   ├── install.sh           # Basic levels installer (Buildroot)
│   ├── TJInstall.sh         # TJ levels installer (Buildroot)
│   ├── fileManipulationSetup.sh  # File manipulation levels (Buildroot)
│   ├── deb_installbandit.sh # Bandit levels (Debian-based systems)
│   └── installbandit.sh     # Bandit levels (Buildroot)
│
├── LEVEL SCRIPTS
│   ├── basic1.sh - basic10.sh      # Basic level generators
│   ├── bandit1.sh - bandit13.sh    # Bandit level generators
│   ├── TJlevel1.sh - TJlevel7.sh   # TJ level generators
│   └── level1FM.sh - level13FM.sh  # File manipulation generators
│
├── UTILITIES
│   ├── profile              # Shell profile (welcome message)
│   ├── nextlevel            # Command to advance levels
│   ├── prevlevel            # Command to go back levels
│   ├── verifyFM.sh          # Verification script
│   └── resources.sh         # Helper functions
│
└── SUPPORT FILES
    ├── masterArray.txt      # Word list for random generation
    └── dictionaries/        # Dictionary files for challenges
```

### How Challenge Generation Works

1. **User Input**: You enter an identifier (email, username, anything)
2. **Hash Creation**: System creates MD5/SHA256 hash from your input + date + secret
3. **Seeded Randomization**: Hash characters determine:
   - Directory names (pulled from `masterArray.txt`)
   - File locations
   - Challenge parameters
4. **Unique Instance**: Result is a challenge set unique to YOU

### Default Credentials

| Item | Value |
|------|-------|
| Game Username | `polylinuxgame` |
| Game Password | `Password1` |
| Level Users | `basic1`, `basic2`, `bandit1`, etc. |
| Level Passwords | Usually empty (no password) |

---

## Setup Options

You have several ways to run this project:

| Option | Difficulty | Hardware | Authenticity |
|--------|------------|----------|--------------|
| A: Raspberry Pi + Buildroot | Hard | Raspberry Pi | Original experience |
| B: Raspberry Pi OS / Debian | Easy | Raspberry Pi or any PC | Works great |
| C: Virtual Machine | Medium | Any computer | Good for testing |

---

## Option A: Raspberry Pi with Buildroot (Advanced)

This is the "authentic" setup - exactly how it was designed to run.

### What You Need

- Raspberry Pi (any model: Zero, 3, 4, 5)
- MicroSD card (8GB minimum, 16GB recommended)
- Power supply for your Pi
- A computer to prepare the SD card
- Ethernet cable OR USB keyboard + HDMI monitor
- Basic Linux knowledge

### Step 1: Download or Build Buildroot

**Option A1: Download Pre-built Image (Easier)**

1. Go to https://buildroot.org/downloads/
2. Look for Raspberry Pi images, OR search for community builds
3. Download an image for your Pi model

**Option A2: Build Your Own (Full Control)**

On a Linux computer (or VM):

```bash
# Install dependencies (Ubuntu/Debian)
sudo apt-get install build-essential libncurses5-dev git bc

# Clone Buildroot
git clone https://github.com/buildroot/buildroot.git
cd buildroot

# Configure for Raspberry Pi (example for Pi 3)
make raspberrypi3_defconfig

# Optional: Customize (add packages, etc.)
make menuconfig

# Build (this takes 30-60 minutes)
make

# Your image will be at: output/images/sdcard.img
```

### Step 2: Flash the SD Card

**On Linux/Mac:**
```bash
# Find your SD card (BE CAREFUL - wrong device = data loss!)
lsblk

# Flash the image (replace sdX with your SD card)
sudo dd if=output/images/sdcard.img of=/dev/sdX bs=4M status=progress
sync
```

**On Windows:**
- Use [balenaEtcher](https://www.balena.io/etcher/) or [Raspberry Pi Imager](https://www.raspberrypi.com/software/)
- Select the `.img` file and your SD card
- Click "Flash"

### Step 3: First Boot & Setup

1. Insert SD card into Raspberry Pi
2. Connect keyboard + monitor OR connect via serial/SSH
3. Power on
4. Log in as `root` (usually no password on fresh Buildroot)

### Step 4: Transfer PolyLinux Game

**Method 1: USB Drive**
```bash
# Mount USB drive
mkdir /mnt/usb
mount /dev/sda1 /mnt/usb

# Copy files
cp -r /mnt/usb/polybandit3 /root/
```

**Method 2: Network (if enabled)**
```bash
# On your computer, use SCP
scp -r polybandit3/ root@<pi-ip-address>:/root/
```

### Step 5: Run the Installer

```bash
cd /root/polybandit3

# For File Manipulation levels:
sh fileManipulationSetup.sh

# OR for TJ levels:
sh TJInstall.sh

# OR for Basic levels:
sh install.sh
```

### Step 6: Play!

After installation completes, you'll be dropped into the game environment automatically.

---

## Option B: Raspberry Pi OS / Debian (Easier)

This is the **recommended option for beginners**. The game works fine on regular Debian-based systems.

### What You Need

- Raspberry Pi (any model) OR any computer
- MicroSD card (for Pi) or hard drive
- Raspberry Pi OS or Debian/Ubuntu installed

### Step 1: Install Raspberry Pi OS

1. Download [Raspberry Pi Imager](https://www.raspberrypi.com/software/)
2. Select "Raspberry Pi OS Lite (64-bit)" - the command-line version
3. Select your SD card
4. Click the gear icon to set:
   - Hostname
   - Enable SSH
   - Set username/password
   - Configure WiFi (optional)
5. Click "Write"

### Step 2: Boot and Connect

1. Insert SD card, power on Pi
2. Connect via SSH: `ssh pi@raspberrypi.local` (or your hostname)
3. Or connect keyboard + monitor directly

### Step 3: Get the Project Files

```bash
# Update system
sudo apt update

# Install git if not present
sudo apt install git -y

# Clone the repository (replace with actual repo URL)
git clone https://github.com/YOUR_USERNAME/polybandit3.git
cd polybandit3
```

Or transfer files via USB/SCP as shown in Option A.

### Step 4: Run the Debian Installer

```bash
# Make scripts executable
chmod +x *.sh

# Run the Debian-compatible installer
sudo sh deb_installbandit.sh
```

**Note**: You may need to modify some scripts slightly for Debian. Key differences:
- Use `useradd` instead of `adduser` (or adjust flags)
- `/home` directory already exists
- Bash is available (not just ash)

### Step 5: Play!

```bash
# Switch to a level user
su - basic1
# or
su - bandit1
```

---

## Option C: Virtual Machine

Run Buildroot in a VM on your regular computer.

### Using QEMU (Free, Cross-Platform)

```bash
# Install QEMU
# Ubuntu/Debian:
sudo apt install qemu-system-arm

# Mac (with Homebrew):
brew install qemu

# Build Buildroot for QEMU
cd buildroot
make qemu_arm_versatile_defconfig
make

# Run it
qemu-system-arm -M versatilepb -kernel output/images/zImage \
  -dtb output/images/versatile-pb.dtb \
  -drive file=output/images/rootfs.ext2,if=scsi,format=raw \
  -append "root=/dev/sda console=ttyAMA0,115200" \
  -nographic
```

### Using VirtualBox/VMware

1. Build Buildroot with x86_64 target:
   ```bash
   make pc_x86_64_efi_defconfig
   make
   ```
2. Convert the image for your VM software
3. Create a new VM and attach the disk image
4. Boot and proceed with installation

---

## Playing the Game

### Basic Commands

| Command | What It Does |
|---------|--------------|
| `nextlevel` | Move to the next level |
| `prevlevel` | Go back one level |
| `cat README.txt` | Read level instructions |
| `ls` | List files in current directory |
| `ls -la` | List ALL files including hidden ones |
| `cd <directory>` | Change to a directory |
| `pwd` | Print current directory |
| `find . -name "filename"` | Search for a file |
| `grep "text" file` | Search for text in a file |
| `cat file` | Display file contents |
| `file <filename>` | Determine file type |
| `strings <filename>` | Extract text from binary files |
| `base64 -d file` | Decode Base64 encoded content |

### Game Flow

1. **Read the README**: Each level has a `README.txt` with instructions
2. **Explore**: Look around using `ls`, `cd`, `find`
3. **Find the Flag**: Usually a password or hidden text
4. **Verify**: Run `verifyFM.sh` (for File Manipulation levels)
5. **Next Level**: Type `nextlevel` to proceed

### Tips

- **Hidden files** start with a dot (`.`). Use `ls -a` to see them!
- **Weird filenames** might have spaces or special characters. Use quotes: `cat "file name"`
- **Binary files** won't display properly with `cat`. Use `strings` instead
- **Stuck?** The answer is always findable with basic Linux commands

---

## Available Challenge Sets

### Basic Levels (1-10)
**Installer**: `install.sh`
**Difficulty**: Beginner
**Skills**: Basic navigation, file operations, simple searching

### Bandit Levels (1-13)
**Installer**: `installbandit.sh` or `deb_installbandit.sh`
**Difficulty**: Beginner to Intermediate
**Skills**: Based on OverTheWire Bandit - file permissions, encoding, binary analysis
**Note**: Inspired by https://overthewire.org/wargames/bandit/

### TJ Levels (1-7)
**Installer**: `TJInstall.sh`
**Difficulty**: Intermediate
**Skills**: Alternative challenge set with varied puzzles

### File Manipulation Levels (1-13)
**Installer**: `fileManipulationSetup.sh`
**Difficulty**: Intermediate to Advanced
**Skills**: Complex directory navigation, file operations in polymorphic structures

---

## Troubleshooting

### "adduser: command not found"

On Debian-based systems, use `useradd` instead:
```bash
# Change this:
adduser -h /home/$userName -D $userName
# To this:
useradd -m -d /home/$userName $userName
```

### "mkdir: cannot create directory '/home': File exists"

This is normal on Debian/Ubuntu - `/home` already exists. The error can be ignored, or add this check to scripts:
```bash
[ -d /home ] || mkdir /home
```

### "passwd: unrecognized option '-d'"

The `-d` flag works differently on different systems:
- Buildroot/BusyBox: `passwd -d username` (delete password)
- Debian: `passwd -d username` (same, but might need sudo)

### Scripts fail with syntax errors

The scripts use `/bin/sh` which may be different shells:
- Buildroot: `ash` (BusyBox)
- Debian/Ubuntu: `dash` (or `bash` if symlinked)

If you see errors about `[[` or other bash-isms, change the shebang:
```bash
#!/bin/bash
```

### Permission denied errors

Make sure you're running as root:
```bash
sudo sh install.sh
```

### Can't switch users (su fails)

Ensure the user was created properly:
```bash
# Check if user exists
id polylinuxgame

# If not, create manually
sudo useradd -m polylinuxgame
sudo passwd polylinuxgame
```

### "nextlevel" command not found

The command wasn't copied to `/usr/bin`:
```bash
sudo cp nextlevel /usr/bin/
sudo cp prevlevel /usr/bin/
sudo chmod 755 /usr/bin/nextlevel /usr/bin/prevlevel
```

---

## Email/Identifier Note

The system asks for a "PSU email" but **does not verify it**. You can enter:
- Any email address
- Any text string
- Just your name

The input is only used to generate your unique challenge hash. There is no network connection or validation.

---

## Credits

- Originally developed at Penn State University
- Designed for cybersecurity education
- Inspired by [OverTheWire Wargames](https://overthewire.org/)

---

## Quick Reference Card

```
+------------------------------------------+
|         POLYLINUX GAME COMMANDS          |
+------------------------------------------+
| nextlevel     - Go to next level         |
| prevlevel     - Go to previous level     |
| cat README.txt - Read instructions       |
| ls -la        - List all files           |
| cd <dir>      - Change directory         |
| pwd           - Print current directory  |
| find . -name X - Find file named X       |
| grep "X" file - Search for X in file     |
| strings file  - Extract text from binary |
| file <name>   - Check file type          |
| base64 -d     - Decode Base64            |
| verifyFM.sh   - Verify level completion  |
+------------------------------------------+
```

---

*Last updated: 2025-11-25*
