## Archlinux Post Install 

**Install yay;**

```
sudo pacman -S --needed git 
git clone https://aur.archlinux.org/yay.git 
cd yay
makepkg -si
```

**Install cinnamon desktop environment;**

```
sudo pacman -S cinnamon nemo-fileroller 
```

**Install lightdm display manager;**

```
sudo pacman -S lightdm lightdm-gtk-greeter
sudo systemctl enable lightdm
```

**Install aditional packages;**

```
sudo pacman -S gnome-terminal firefox vlc pulseaudio pulseaudio-alsa pavucontrol
```

**Pacman enabling parellel downloads and colour;**

Edit vim /etc/pacman.conf, uncomment ParallelDownloads and Color

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
**User directories;**

```
sudo pacman -S xdg-user-dirs
xdg-user-dirs-update
```
**Setting the framebuffer resolution and disable GRUB delay;**

To view the list of supported modes,

```
sudo hwinfo --framebuffer
```

Edit /etc/default/grub

```
GRUB_TIMEOUT_STYLE=hidden
GRUB_GFXMODE=1920x1080x24
GRUB_GFXPAYLOAD_LINUX=keep
```
Update grub config

```
sudo grub-mkconfig -o /boot/grub/grub.cfg
```
