root@archiso ~#

Internet connection

	# wifi-menu
	# ping -c 3 google.com

Verifying EFI

	# efivar -l

Disk partitions

	# lsblk
	
Wiping the existing partitions

	# gdisk /dev/sda
		x
		z
		y
		y

	# cgdisk /dev/sda
		boot:	First sector:	Leave blank
			Size sector:	1024M
			Hex code:	EF00
			Name:		boot

		swap:	First sector:	Leave blanl
			Size sector:	16G	->	(2x RAM)
			Hex code:	8200
			Name:		swap

		root:	First sector:	Leave blank
			Size sector:	53G
			Hex code:	Leave blank
			Name:		root

		home:	First sector:	Leave blank
			Size sector:	Leave blank
			Hex code:	Leave blank
			name:		home

		[write]
		y
		[exit]

Telling linux use the partitions

	# mkfs.fat -F32 /dev/sda1	->	boot
	# mkswap /dev/sda2		->	swap
	# swapon /dev/sda2		->	swap
	# mkfs.ext4 /dev/sda3		->	root
	# mkfs.ext4 /dev/sda4		->	home

Mount partitions

	# mkdir /mnt			->	just check
	# mount /dev/sda3 /mnt
	# mkdir /mnt/boot
	# mkdir /mnt/home
	# mount /dev/sda1 /mnt/boot
	# mount /dev/sda4 /mnt/home

Seting up repository mirrorlist

	# cp /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.backup
	# sed -i 's/^#Server/Server/' /etc/pacman.d/mirrorlist.backup
	# rankmirrors -n 6 /etc/pacman.d/mirrorlist.backup > /etc/pacman.d/mirrorlist

Instaling arch base

	# pacstrap -i /mnt base base-devel

Fstab file

	# genfstab -U -p /mnt >> /mnt/etc/fstab
	# nano /mnt/etc/fstab		->	verifing partitions

Chroot

	#arch-chroot /mnt

	# nano /etc/locale.gen		->	en_US.UTF-8
	# locale-gen
	# echo LANG=en_US.UTF-8 > /etc/locale.conf
	# export LANG=en_US.UTF-8

	# ls /usr/share/zoneinfo
	# ln -s /usr/share/zoneinfo/Brazil/East > /etc/localtime
	# hwclock --systohc --utc

	# excho 'hostname' > /etc/hostname

Enabling multilib and AUR comunity repositories

	# nano /etc/pacman.conf		->	un-coment the multilib repo
		add AUR repo:	[archlinuxfr]
				SigLevel = Never
				Server = http://repo.archlinux.fr/$arch

Update

	# pacman -Suy

Instal yaourt

	# pacman -S yaourt

Root an user

	# passwd
	# useradd -m -g users -G wheel,storage,power -s /bin/bash 'nome_de_usuario'
	# passwd 'nome_de_usuario'
	# EDITOR=nano visudo
		uncomment:	%wheel ALL=(ALL)ALL
		add:		Defaults rootpw

Bash completion commands

	# pacman -S bash-completion

Bootloader

	# mount -t efivarfs efivarfs /sys/firmware/efi/efivars
	# bootctl install	-> already in system, just 'make install'
		verify:	# ls /boot

	# nano /boot/loader/entries/arch.conf	->	new file
		write:	title Arch Linux
			linux /vmlinuz-linux
			initrd /initramfs-linux.img
			options root=/dev/sda3 rw

	# nano /boot/loader/loader.conf
		write:	timeout 3
			default arch

Wifi-Menu

	# pacman -S wpa_supplicant wpa_actiond dialog

	# exit
	# umount -R /mnt
	# reboot

Interface and Desktop environment

Internet connection (no wifi)

	$ ip link
	$ sudo ip link set interface up
	$ sudo systemctl enable dhcpcd@interface.service

Touchpad support

	$ sudo pacman -S xf86-input-synaptics

X

	$ sudo pacman -S xorg-server xorg-server-utils xorg-xinit xorg-twm xorg-xclock xterm

Graphics driver

	$ sudo pacman -S xf86-video-vesa 	-> Genérico

Gnome

	$ sudo pacman -S gnome gnome-extra
	$ sudo pacman -S gdm
	$ sudo systemctl enable gdm.service
	$ sudo pacman -S gnome-tweak-tool
	$ yaourt -S gnome-software

i3wm

	$ sudo pacman -S i3 dmenu

Reboot

	$ sudo reboot
