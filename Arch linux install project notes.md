1) I downloaded an ISO mirror from the mit.edu repo
2) I created a obsidian account and intend to sign-up for a student discount
3) read over the FAQ's and hashed the download using 'certutil' in cmd prompt to verify.
4) When starting the Arch install process for the first time
	1) allow for several hours for each attempt, with access to charging for the laptop and sufficient space to write handwritten notes (to free up VM host system resources). these handwritten notes will later be moved to markdown.  
	2) Things to notice based on the install guide, The install looks to be easy initially, but once installation gets to the point of configuring hardware and drivers necessary for downloading updates, things will become more complicated, as the commands will be more involved.  
	3) NOTE the arch linux install page itself is simple, HOWEVER it's similar to the trunk of a tree, BRANCHES OUT HEAVILY AND IS EASY TO GET LOST IN.  focus on what task at hand in the install guide, based on the install step that has been reached, but document the steps back to the install guide, as the branches from the Arch Linux wiki are rabbit holes. Getting lost in Arch Linux wiki can lead to doing the wrong things, causing problems, or doing unnecessary steps. 

5) Starting first install attempt - using virtualBox, allocating 2 CPU'S 12 gb ram, 40 gb drive space(VDI), enabling efi. 
	1) arch linux booted, I set the key layout to US, 
		2) 'setfont ter-124b' , after some tinkering with the archlinux suggestions THIS ONLY APPLIES TO THE SETUP DURING THE ISO. 
	2) I stumbled around a bit with the console screen not auto adjusting, need to be aware of this issue. 
	3) Struggling with rfkill, how to list installed adapters. UNNECESSARY STEP
	4)iwctl cmd is taking some online research to navigate. UNNECESSARY STEP
6) shut down the machine to go to class. machine only has an issue after the first reboot.  delete saved state after the first shut-down.  will be starting from scratch every time until fully installed. 
7) I re-enabled my network adapters for virtualBox on my laptop. 
	1) I re-started the VM, changing to paravirtualization. UNNECESSARY STEP
	2) paravirtualization isn't necessary, despite my network adapter not being detected initially. UNNECESSARY STEP
	3) pinged "ping.archlinux.org" 0% packet loss, 6 packets TX, 6 Rx 8011ms
8) checked synch of time clock with 'timedatectl'  time, local system time not right. referred to 'man timedatectl' to find 'timedatectl list-timezones' then 'timedatectl set-timezone America/Chicago' to adjust to central time.
9) used lsblk to find that the 40Gb that I allocated to disk is on sda.  partition already created WRONG WRONG WRONGF
	1) used 'umount /dev/sda' to unmount partition WRONG
	2) used 'mkfs.ext4 /dev/sda' to turn the unmounted sda partition into an ext4 partition. WRONG
	3) used 'mount /dev/sda mnt' to mount the newly created partition. WRONG
	4) sda partition now has a UUID and FSTYPE when under the 'lsblk -f' command WRONG
10) I'm to the essentials part.  I'm attempting to download mirrors.  
	1) I need to get reflector service running. Got reflector running with 'systemctl start reflector.service'
	2) unable to download intel microcode using 'pacman -S intel-ucode' will be using '-Sy' to force a new update from the repository.  forced new update, update failed
	3) attempted ext4 file system update using 'pacman -Sy e2fsprogs' package is up to date, didn't install. 
	4) updated standard linux firmware package with 'pacman -S linux-firmware'
	5) updated coreutils pacman -S coreutils'
	6) attempted video firmware update with 'pacman -S linux-firmware-intel' errors, update failed. 
	7) ran 'pacman -Syyu' to force a sync and update to the mirrors that the system is using. 
11) tried using genfstab -U /mnt >> /mnt/etc/fstab' to mark a partition for startup unable, I needed to create a genfstab file based on UUID 
	1) ALL STEPS FAILED BECAUSE OF THE PARTITION ERROR I CREATED IN STEP 9
		1) manually created a dir in the /mnt/etc/ directory to use the command 'genfstab -U /mnt >> /mnt/etc/fstab'  created the directory with 'mkdir -p /mnt/etc/' 
		2) after running the genfstab command, checked its output using nano. 
		3) this FAILED to persist across restarts, HOWEVER the partition itself does persist, meaning im making progress in configuring the OS. 
		4) I cannot get the boot partition to run on startup, I'm going to first attempt configuring the 'efivarfs' system, 
12) BELIEVE TO BE A PARTITION ERROR, ERASING THE VM and start over, I have saved the a quick reference of commands to speed things up for the next round.  REFERENCE ERRORS IN  STEP 9
	1) starting again, configured keyboard type, font size, checked time zone (didn't adjust to central), started reflector.service, going to partition AND format the drives this time. 
	2) restarted the instance, going to start with the partition from this attempt
	3) this time around, went into the fdisk utility and performed the following actions:
		1) created a GPT file system table
		2) created a type 20 (linux file system) partition on sda1, size 2Gib ( I meant to size it for 4 Gb, but my sector math was wrong)
		3) created a type 19 (linux swap) partition on sda2, 6Gb, I wasn't sure about my math, winged it and got 6 gb.
		4) created a type 1 (EFI System) on sda3, used the remaining for it size was 32 Gb. 
		5) I'm going to format all 3, then mount.
			1) mounting is being problematic, having to look through 'man mount' to figure some things out. 
			2) trying the route of '--mkdir /dev/ [partition] /mnt/boot'
			3) '--mkdir /dev/ [partition] /mnt/boot' worked for sda1 (boot part) and sda3 (main partition) used 'swapon' for the sda2 swap partition
				1) mount point for main part was WRONG need sda3 to be /mnt/home
				2) going to leave boot FS and main Part as Linux file system. 
			4) it seems to work, because they are all in the /dev dir and highlighted yellow.  
			5) the partitions persisted through a reboot and a VM instance restart. 
		6) moving on to downloading updates and other packs
			1) attempted the 'pacman -Syyu' command, there's 5.4 Mib of updates, however, I lack the space needed.  I made my partitions too small, going on without them.  looking at the list of updates, I believe a lot of the updates are unnecessary. 
13) DELETED THE VM, STARTED OVER for the sake of having larger boot partitions and swap partitions.  I corrected my math, mostly, this time around.  I gave the VM 55Gb to work with.  
	1) used the '+size' denotation for end of the sector sizing to get around end sector calculations.  
	2) proceeding with modifications to drive mounting this time around.  
	3) mounted partitions sda1 and sda3 with 'mount' options --mkdir /dev/ sda1 /mnt/boot/'  and '--mkdir /dev/sda3 /mnt/home' and 'swapon /dev/sda2' for sda2 for my swap partition. they do not persist across reboots. 
	4) Changed type of sda1 partition to EFI to make it compatible with fat32. 
		1) mounted sda3 as 'mount /dev/sda3'
		2) mounted 'mount --mkdir /dev/sda1 /mnt/boot'
		3) mounted sda2 swap partition using 'swapon /dev/sda2'
	5) used 'pacstrap -K /mnt base linux linux-firmware intel-ucode nano networkmanager' to install microcode updates network manager. 
	6) checked fstab file with 'cat /mnt/etc/fstab' configureation okay.
	7) executed command ' arch-chroot /mnt' to enter the system
	8) used 'pacman -S sudo' to install sudo into system
	9) modified the system time with 'ln -sf /usr/share/zoneinfo/_Region_/_City_ /etc/localtime'
	10) ran 'hwclock --systohc' to generate /etc/adjust time
	11) set root password using 'passwd'
	12) installed GRUB bootloader using 'grub-install --target=x86_64-efi --efi-directory=boot --bootloader-id=GRUB'
		1) deleted '/efi' and '/boot' dir to uninstall GRUB, reinstalled GRUB using 'grub-install --target=x86_64-efi --efi-directory=efi/boot --bootloader-id=GRUB'
		2) changed boot directory from /dev/sda1 to /boot/efi' using 'mount --mkdir /dev/sda1 /boot/efi'
		3)rebooting to see if boot manager takes. 
			1) booted only to GRUB
		3) mount /dev/sda1 /boot
			1) grub-mkconfig -o /boot/grub/grub.cfg
		13)successful bootloader to arch after reboot, got help from clark. 
14) after some refresher , I figured out that the standard password is 'root' and I have set the password. 
15) I used 'ip link --help' to access the 'ip menu'
	1) used 'ip link show' to discover that the ip link device for my Arch VM instance is enp0s3.
		1) enp0s3 is in the DOWN state.
	2) used 'ip link set enp0s3 up' to enable the device.
16) configuring the routing table. attempted to see if there is an existing ipv4 route using 'ip route show' no return', attempted to see if there is an existing dhcp configuration using 'dhcping' command not found returned. 
	1) started systemd-networkd.service using 'systemctl start systemd-networkd.service'
	2) entered 'networkctl status'
		1) interfaces 1,2 - state routable; online - unknown; Address - fd17:625:f037:2:a00:27ff:fe24:45db: on enp0s3  (another non broadcast listed); Gateway - fe80:: 2: on enp0s3
	3) to address DNS name resolution, created sym link using 'ln -sf ..run.systemd/resolve/stub-resolv.conf /etc/resolv.conf' 
		1) realized previous syntax error, re-entered  corrected command, despite zsh accepting previous command 'ln -sf ..run/systemd/resolve/stub-resolv.conf /etc/resolv.conf' 
		2) previous commands entered in installation framework
		3) 'network status' now shows interfaces 1,2; state - off; Online state - unknown
		4) entered 'echo >> [Resolve] FallbackDNS=127.0.0.1 ::1'
	4) rebooted, checked ip link network enp0s3 (ethernet) was DOWN. used 'ip link set enp0s3 up' then 'networkctl up enp0s3'.  
		1) checked NetworkManger Status with 'systemctl status NetworkManager'  
			1) network manager shows loaded; disabled; preset: disabled
		2) issue was caused by not enabling Network manager during the install process prior to booting GRUB.  went back into into Arch iso, downloaded networkmanager AGAIN using 'pacman -S networkmanager', then enabled it using 'systemctl enable NetworkManager.service' and set NetworkManager to start for the current session using 'systemctl start NetworkManager.service'
		3) enabled and started DNS resolver services 'systemctl enable systemd-resolved'  and 'systemctl start systemd-resolved'  after doing this, a ping to an FQDN is translated to an IP address, pinged and returned successfully. 
17) used 'bash --version' to check shell version. confirmed shell is bash 
	1) downloaded and installed zsh using 'pacman -S zsh-completions zsh-syntax-highlighting'
	2) initially tried to change root shell to zsh, but failed, decided to create user 'josh' with home directories using 'useradd -m -s /bin/bash josh' 
	3) tried using 'chsh -s /usr/bin/zsh' to change root shell from bash to shell, but got messages of shell changing that ended in failure.
	4) successfully changed root shell with 'chsh -s /bin/zsh', zsh shell setting persists after VM shutdown and restart. 
	5) verified internet connection with 'ping 1.1.1.1' verified DNS resolution with 'ping www.google.com', both commands resulted in 0% packet loss. 
STARTED WORKING ON PROJECT AGAIN, CHANGED FONT TO ' using setfont /usr/share/kbd/consolefonts/iso02-12x22.psfu.gz'
18) Created user 'josh' and added to the 'wheel' group, user password set and saved in separate obsidian file
19) Created user 'Codi' and added to the 'wheel' group, user password set and saved in separate obsidian file. 
20) looking how to properly add users 'Josh' and 'Codi' the only users with sudo privileges. Both users have passwords set.   likely editing sudoers file using VISUDIO. 
	1) having issues with editing in visudo, as there's no text editor set for visudo, creating a link for visudo to use nano
		1) created a sym link from nano to visudo using 'ln -s /usr/bin/nano /usr/bin/vi'
	2) removed # comment from '%sudo ALL=(ALL:ALL) ALL' in visudo
		1) didn't work as the sudo group doesn't exist in Arch Linux, 
	3) removed # comment from '%wheel ALL=(ALL:ALL) ALL' as Arch Linux uses the wheel group as the sudo group. 
21) downloaded and installed 'xorg-server' initially had some issues with the download failing at the end, believe it was due to a need to refresh/resync the package servers.  initially I was using 'pacman -S xorg-server', then I did 'pacman -Sy' to force a resync. I then did 'pacman '-Sy xorg-server' to download, this time the download didn't fail at the end. 
22) Will be attempting a graphical GDM login interface next. 
23) installed gdm as user 'josh' using 'sudo pacman -Sy gdm' I was prompted about what repositories to use on 2 different occasions, the first selection I used was 'jack2' repository. I missed the second request I missed and just selected the default choice again. 
	1) this is a 1.2 Gib install.  install successful, next will be configuring for autorun
24) installed the GNOME desktop environment using command 'pacman -Sy gnome' after configuration, I will save a snapshot of the VM, and enable autostart. 
25) adjusting both user default shells for zsh. doing this by going into the vi /etc/passwd file, going to the line with users 'josh' and 'Codi' and changing the end of the line /home/josh: /bin/bash' to 'home/josh: /bin/zsh'
	1) did the same for user 'Codi' I will perform default or minimal zsh configurations for user 'Codi'
		1) issue with user name not showing up in terminal when logged in
		2) 