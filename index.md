## Arch Linux Install Tutorial

### Setting the System Time
`timedatectl set-ntp true`

`timedatectl set-timezone US/Central`

### Partitioning the Hard Disk
`fdisk -l    # Shows Block Devices, /dev/sda should be there`

`fdisk /dev/sda`

set part table as GPT with 'g'

create partition 1
* use default first sector
* set part 1 to +1M
* change type with 't'
* set type to 'BIOS boot'

create partition 2
* use default first sector
* set part 2 to +4G
* change type with 't'
* set type to 'swap'

create partition 3
* use default first sector
* use deafult last sector, setting part 2 to rest of Disk

w to exit

#### Formatting Partitions

`mkfs.ext4 /dev/sda3`

`mkswap /dev/sda2`

### Mounting Partitions

`mount /dev/sda3 /mnt`

`swapon /dev/sda2`

### Installing Base Software

`pacstrap /mnt base linux linux-firmware iproute2 net-tools dhcpcd ufw vim`

### Configure the System

`genfstab -U /mnt >> /mnt/etc/fstab`

#### Chroot

`arch-chroot /mnt`

#### Time Zone

`ln -sf /usr/share/zoneinfo/US/Central /etc/localtime`

`hwclock --systohc`

##### Localization

Open `/etc/locale.gen` and uncomment "en_US.UTF-8 UTF-8"

`locale-gen`

`echo "LANG=en_US.UTF-8" > /etc/locale.conf`

##### Network Configuration

`hostnamectl set-hostname Arch-CS3353`

#### Initramfs

`mkinitcpio -P`

#### Root password

`passwd`

#### Boot Loader

`pacman -S grub`

`grub-install /dev/sda`

`grub-mkconfig -o /boot/grub/grub.cfg`

`exit`

`umount -R /mnt`

`reboot`

### Setting Up New System

Boot into 'Arch Linux' entry of GRUB menu


#### Setting Up DHCP Client

`systemctl start dhcpcd && systemctl enable dhcpcd`

`ip link set ens33 up`

`ping google.com`

#### Set Up Users

`pacman -S sudo`

`groupadd sudo`

`sudo EDITOR=vim visudo`

Uncomment the line that reads `%sudo ALL=(ALL) ALL`

Create user 'eric' with new home directory and add them to group 'sudo'

`useradd -m -G sudo eric`
`useradd -m -G sudo codi`
`useradd -m -G sudo sal`

Set password for users to 'GraceHopper1906`
`passwd eric`
`passwd codi`
`passwd sal`

#### Install ZSH with Oh My ZSH

`pacman -S curl wget git zsh`

`sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"`

`git clone https://github.com/zsh-users/zsh-autosuggestions.git $ZSH_CUSTOM/plugins/zsh-autosuggestions`

`git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting`

Import eric's custom zshrc

`curl -o ~/.zshrc https://ericiniguez.codes/file/zshrc`

Edit the .zshrc to have your home directory's .oh-my-zsh directory instead on line 4

Import eric's custom vimrc 

`curl -o ~/.vimrc https://ericiniguez.codes/file/vimrc`

#### Install SSH

`pacman -S openssh`

#### Installing a Desktop Environment (GUI)

`pacman -S xorg xorg-server gnome gnome-packagekit gnome-software-packagekit-plugin firefox leafpad ttf-cascadia-code`

Press Enter to accept default options for all prompts

`systemctl enable gdm.service && systemctl start gdm.service`

####