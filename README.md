# archlinux

UEFI+GPT (-BIOS(GRUB)+MBR)    

parted /dev/mmcblk0  
    
    (parted) mklabel gpt
    (parted) mkpart ESP fat32 1MiB 513MiB
    (parted) mkpart primary ext4 513MiB 100%
    (parted) set 1 boot on
    (parted) p
    (parted) q


lsblk -f /dev/mmcblk0

    /dev/mmcblk0              disk    MOUNTPOINT
    ├─   mmcblk0p1    vfat    part    /boot    
    └─   mmcblk0p2    ext4    part    /    


mkfs.fat -F32 /dev/mmcblk0p1   
mkfs.ext4 /dev/mmcblk0p2

mount /dev/mmcblk0p2 /mnt   
mkdir /mnt/boot   
mount /dev/mmcblk0p1 /mnt/boot   

wifi-menu

pacstrap -i /mnt base base-devel

genfstab -U -p /mnt >> /mnt/etc/fstab 


arch-chroot /mnt /bin/bash  

vi /etc/locale.gen   
locale-gen    
echo LANG=en_US.UTF-8 > /etc/locale.conf   

ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime     
hwclock --systohc --utc   

echo lts > /etc/hostname    
vi /etc/hosts    


pacman -S intel-ucode dialog wpa_supplicant    

[ramdisk]   
mkinitcpio -p linux   

[Bootloader] (UEFI+GPT) - systemd    
bootctl install    
vi /boot/loader/entries/arch.conf    

    title Arch Linux
    linux /vmlinuz-linux
    initrd /intel-ucode.img
    initrd /initramfs-linux.img
    options root=/dev/mmcblk0p2 rw

exit    
umount -R /mnt    

reboot    

-------------------------------

vi /etc/pacman.conf

    [archlinuxcn]
    SigLevel = Optional TrustAll
    Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch

pacman -Syu yaourt   
pacman -S archlinuxcn-keyring    

[xorg]   
pacman -S xorg-server xorg-xinit xorg-twm xorg-xclock xterm   
startx   

pacman -S openbox feh xfce4-terminal alsi screenfetch firefox wqy-zenhei    

alias ls='ls --color'   
feh --bg-scale /PATH/arch.jpg   
xinit /usr/bin/openbox-session   
