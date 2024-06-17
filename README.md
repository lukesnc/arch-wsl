# Arch on WSL

This is how I get my instance of Arch working on Windows Subsystem for Linux. Unlike many Arch WSL solutions, this guide uses WSL to import the Arch Linux docker image as a WSL instance.

## Requirements

- Windows 11 x64
- Windows Subsystem for Linux installed

If Windows Subsystem for Linux is not installed, from PowerShell run:

```powershell
wsl --install --no-distribution
```

## Setup

### Step 1

Download the latest build of the [Arch Linux Docker image](https://gitlab.archlinux.org/archlinux/archlinux-docker/-/releases). I usually grab `base-devel`.

### Step 2

Since we're not using the Microsoft store to manage our instance, we will make a directory for our Arch setup to live in. I will be using `C:\Arch\`. From PowerShell run:

```powershell
New-Item -Path "C:\Arch" -ItemType Directory -Force
```

Move the Docker image we just downloaded into this folder. We now have something like this (the image will be the one you chose from GitLab):

```
PS C:\> ls .\Arch\


    Directory: C:\Arch


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         6/16/2024   7:47 PM      213692977 base-devel-20240616.0.241671.tar.zst
```

### Step 3

Import the image into WSL:

```powershell
wsl --import Arch C:\Arch\ C:\Arch\YOUR_ARCH_IMAGE.tar.zst
```

You can now launch into your instance:

```powershell
wsl -d Arch
```

## Post-install

From here Arch is now working but there's a few quirks because we used the Docker image. The steps prior to switching to our newly created user will be performed as root.

### Configure pacman

In `/etc/pacman.conf` I like to enable `Color` and disalbe `NoProgressBar` and `VerbosePkgLists`. This way it looks more like how pacman comes by default on desktop.

### Generate locale

You can change this to a different locale if you'd like.

```bash
sed -i '/^# *en_US.UTF-8 UTF-8/s/^# *//' /etc/locale.gen
locale-gen
```

### Make a user

Start by installing `sudo` and an editor:

```bash
pacman -S sudo neovim
groupadd sudo
EDITOR=nvim visudo
```

Once sudo is configured, make your user account. Change `myUsername` to whatever you'd like.

```bash
myUsername=user
useradd -m -G sudo $myUsername
echo -e "[user]\ndefault=$myUsername" >> /etc/wsl.conf
passwd $myUsername
```

This also makes our new user the default user on launch. We can now switch to our user.

```bash
su $myUsername
```

### Shortcut to Windows home directory

Sometimes I need to hop into my Windows user's `Downloads/` or something, so this shortcut comes in handy. If your Arch user's name is not the same as your Windows user's name you will need to edit this manually.

```bash
ln -sf "/mnt/c/Users/$USER" ~/winhome
```

### AUR helper

Life is better with an AUR helper. I like [Paru](https://github.com/Morganamilo/paru). [Yay](https://github.com/Jguer/yay) is also a great option.

## Done

Now even Windows users can say they use Arch (btw)

### Help / Additional Reading

- <https://learn.microsoft.com/en-us/windows/wsl/use-custom-distro>
- <https://wiki.archlinux.org/title/users_and_groups>
