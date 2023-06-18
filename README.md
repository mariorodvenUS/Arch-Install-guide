# Arch-Install-guide

## Teclas en español:

    loadkeys es

## Particiones:
    lsblk
    cfdisk
En cfdisk creamos las particiones, dejamos una para la swap, otra para el boot y la principal, de unos 16Gb, 1Gb y el resto del tamaño respectivamente.

En la particion de boot selecionamos EFI System en el tipo, en el swap, seleccionamos Linux Swap y en la ultima seleccionamos Linux filesystem

## Formatear particiones:
    mkfs.fat -F32 /dev/sda1     //La de boot
    mkfs.ext4 /dev/sda3         //La mas grande
    mkswap /dev/sda2            //La de swap
    swapon /dev/sda2

## Montar particiones:
    mount /dev/sda3 /mnt
    mkdir /mnt/boot
    mount /dev/sda1 /mnt/boot

## Instalar paquetes base:
    pacstrap /mnt base base-devel linux linux-firmware vim nano

## Generar fstab:  
    genfstab -U /mnt >> /mnt/etc/fstab

## Hacemos chroot:
    arch-chroot /mnt

## Configurar zona horaria:
    ln -sf /usr/share/zoneinfo/Europe/Madrid /etc/localtime
    hwclock --systohc

## Configurar idioma:
    vim /etc/locale.gen
    Descomentamos la linea de es_ES.UTF-8 UTF-8
    locale-gen
    
## Creamos los usuarios del sistema:
    useradd -m mario
    usermod -aG wheel mario

Editamos el archivo de sudoers:
    vim /etc/sudoers
    Descomentamos la linea %wheel ALL=(ALL) NOPASSWD: ALL

## Configuramos el bootloader:
    sudo pacman -S grub efibootmgr
    grub-install /dev/sda   //Instalamos grub en el disco
    grub-mkconfig -o /boot/grub/grub.cfg   //Generamos la configuracion de grub

## Le damos un nombre a nuestra maquina:
    echo "demonArch" > /etc/hostname

## Configuramos el archivo hosts:
    vim /etc/hosts
    Añadimos:
    127.0.0.1   localhost
    ::1         localhost
    127.0.0.1   demonArch.locahost   demonArch

## Salimos del chroot y reiniciamos:
    exit
    umount -R /mnt
    reboot