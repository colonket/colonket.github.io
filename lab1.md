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

Use mkfs and mkswap to format the corresponding partitions into ext4 and swap partitions

`mkfs.ext4 /dev/sda3`

`mkswap /dev/sda2`

### Mounting Partitions

Mount the installation's primary partitition to start setting up the system's files

`mount /dev/sda3 /mnt`

Turn on the linux swap memory of the installation's swap partition

`swapon /dev/sda2`

### Installing Base Software

Install software to the installation's file system

`pacstrap /mnt base linux linux-firmware iproute2 net-tools dhcpcd ufw vim`

### Configure the System

Tell the system where file systems and data are

`genfstab -U /mnt >> /mnt/etc/fstab`

#### Chroot

Change your linux instance's root directory to be that of the installation's root directory

`arch-chroot /mnt`

#### Time Zone

Set the installation's time zone

`ln -sf /usr/share/zoneinfo/US/Central /etc/localtime`

`hwclock --systohc`

##### Localization

Establish the character encoding for the language and characters you want your system to use

Open `/etc/locale.gen` and uncomment "en_US.UTF-8 UTF-8"

`locale-gen`

`echo "LANG=en_US.UTF-8" > /etc/locale.conf`

##### Network Configuration

Set the hostname

`hostnamectl set-hostname Arch-CS3353`

#### Initramfs

Generate the initial system that gets loaded on boot-up

`mkinitcpio -P`

#### Root password

Set the password for 'root'

`passwd`

#### Boot Loader

This is what will let you boot into your system, without this your system will not boot.

`pacman -S grub`

Point grub to the storage device with your installation on it

`grub-install /dev/sda`

Tell grub where your operating system is on the storage device

`grub-mkconfig -o /boot/grub/grub.cfg`

Exit the chroot Environment

`exit`

Unmount the installation's storage device

`umount -R /mnt`

Reboot the system

`reboot`

### Setting Up New System

Boot into 'Arch Linux' entry of GRUB menu


#### Setting Up DHCP Client

Use DHCP to be able to acquire a DHCP IP address lease on a network with DHCP

`systemctl start dhcpcd && systemctl enable dhcpcd`

Start your network interface

`ip link set ens33 up`

Check connectivity

`ping google.com`

#### Set Up Users

Install the sudo tool

`pacman -S sudo`

Create the sudo group

`groupadd sudo`

Edit the sudoers file

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

Install the neccesary packages

`pacman -S curl wget git zsh`

Install oh my zsh

`sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"`

Clone oh my zsh plugins

`git clone https://github.com/zsh-users/zsh-autosuggestions.git $ZSH_CUSTOM/plugins/zsh-autosuggestions`

`git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting`

Import eric's custom zshrc

`curl -o ~/.zshrc https://ericiniguez.codes/file/zshrc`

Edit the .zshrc to have your home directory's .oh-my-zsh directory instead on line 4

Import eric's custom vimrc 

`curl -o ~/.vimrc https://ericiniguez.codes/file/vimrc`

#### Install SSH

Install SSH

`pacman -S openssh`

#### Installing a Desktop Environment (GUI)

Install the Xorg Display Server, the Gnome Desktop Environment, the Gnome Software Package Plugin to fix the Graphic Software Package Manager,
Firefox Web Browser, Leafpad Graphical Text Editor, and Cascadia Code Font.

`pacman -S xorg xorg-server gnome gnome-packagekit gnome-software-packagekit-plugin firefox leafpad ttf-cascadia-code`

Press Enter to accept default options for all prompts

Enable the Gnome Display Manager to start when you start the computer, then start it manually right now

`systemctl enable gdm.service && systemctl start gdm.service`

### Enjoy your new Arch Linux System!