## Archlinux Post Install 

**Check for pacman package updates**

```
sudo pacman -Syyu
```
**Install reflector;**

```
sudo pacman -S reflector
```

- Backup mirrorlist file;

```
sudo mv /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.orig
```

- Get mirrorlist by region India, latest and fastest number of 10 and sort by rate;

```
sudo reflector --country 'India' --latest 10 --fastest 10 --protocol https --sort rate --save /etc/pacman.d/mirrorlist
```

**Install cinnamon desktop environment;**

```
sudo pacman -S cinnamon nemo-fileroller 
```

**Install i3 desktop environment;**

```
sudo pacman -S i3 dmenu
```

**Install lightdm display manager;**

```
sudo pacman -S lightdm lightdm-gtk-greeter
sudo systemctl enable lightdm
```

- Configure webkit2 greeter edit  `sudo vim /etc/lightdm/lightdm.conf`, search for "#greeter-session=example-gtk-gnome" uncomment

```
greeter-session=lightdm-webkit2-greeter
``` 

**Install aditional packages;**

```
sudo pacman -S gnome-terminal firefox vlc audacious ntfs-3g kdenlive handbrake obsidian audacity intel-ucode pulseaudio pulseaudio-alsa 
```
**Pacman enabling parellel downloads and colour;**

- Edit `sudo vim /etc/pacman.conf`, uncomment ParallelDownloads and Color

```
# Misc options
#UseSyslog
Color
#NoProgressBar
CheckSpace
#VerbosePkgLists
ParallelDownloads = 5
```
**Pacman cleaning the package cache;**

```
sudo pacman -S pacman-contrib
sudo systemctl enable paccache.timer --now
```
**Create user directories;**

```
sudo pacman -S xdg-user-dirs
xdg-user-dirs-update
```
**Setting the framebuffer resolution and disable GRUB delay;**

- To view the list of supported modes,

```
sudo hwinfo --framebuffer
```

- Edit `sudo vim /etc/default/grub`

```
GRUB_GFXMODE=1920x1080x24
GRUB_GFXPAYLOAD_LINUX=keep
```

- Update grub config

```
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

**Install yay;**

```
sudo pacman -S --needed git 
git clone https://aur.archlinux.org/yay.git 
cd yay
makepkg -si
```
- Check for yay package updates;

```
yay -Syyu
```

**Install mint themes and icons;**

```
yay -S mint-themes mint-x-icons mint-y-icons
```

***Install google chrome;

```
yay -S mint-themes mint-x-icons mint-y-icons
```
