# Arch on WSL

This is how I get my instance of Arch working on Windows Subsystem for Linux.

## Setup

### Step 1

Download the latest build of the [Arch Linux Docker image](https://gitlab.archlinux.org/archlinux/archlinux-docker/-/releases). I usually grab `base-devel`.

### Step 2

We will make a directory for our instance to live in. I like `C:\Arch\`. From PowerShell:

```powershell
New-Item -Path "C:\Arch" -ItemType Directory -Force
```

Move the Docker image we just downloaded into this folder.

### Step 3

Import the image into WSL:

```powershell
wsl --import Arch C:\Arch\ C:\Arch\YOUR-ARCH-IMAGE.tar.zst
```

You can now launch into your instance:

```powershell
wsl -d Arch
```

## Post-install

From here Arch is now working but there's a few quirks because we used the Docker image.

### Init pacman

A working package manager is good.

```bash
pacman-key --init
pacman -Sy archlinux-keyring
```

In `/etc/pacman.conf` I like to enable `Color` and disalbe `NoProgressBar` and `VerbosePkgLists`.

### Generate locale

You can change this if you want a different locale.

```bash
sed -i '/^# *en_US.UTF-8 UTF-8/s/^# *//' /etc/locale.gen
locale-gen
```

### Make a user

Start by installing sudo and an editor:

```bash
groupadd sudo
pacman -S sudo neovim
EDITOR=nvim visudo
```

Once sudo is configured make a user:

```bash
myUsername=user
useradd -m -G sudo $myUsername
echo -e "[user]\ndefault=$myUsername" >> /etc/wsl.conf
passwd $myUsername
```

This also makes our new user the default user on launch. We can now switch to our user.

```
su $myUsername
```

### AUR helper

Grab an AUR helper. I like [paru](https://github.com/Morganamilo/paru).

