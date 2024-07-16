# arch-t580
Arch Linux Install guide, LVM on LUKS, btrfs, Unified Kernel Image, bootctl 

cgdisk /dev/nvme0n1

### Create 1 GiB efi paritiıon (ef00) Label boot
### Create 100% 8e00 partition Label luks
### Create a physical volume on top of the opened LUKS container:
cryptsetup luksFormat --type luks1 /dev/nvme0n1p6
cryptsetup open /dev/nvme0n1p6 cryptlvm
cryptsetup luksChangeKey /dev/nvme0n1p6 -S 0 --pbkdf=pbkdf2

pvcreate /dev/mapper/cryptlvm

# Create a volume group (in this example named vg1, but it can be whatever you want) and add the previously created physical volume to it
vgcreate vg1 /dev/mapper/cryptlvm

lvcreate -L 40G vg1 -n root
lvcreate -L 8G vg1 -n swap
lvcreate -l 100%FREE vg1 -n home

# Format filesystems 
mkfs.vfat -F32 /dev/nvme0n1p5
