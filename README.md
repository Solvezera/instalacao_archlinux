# PASSO A PASSO DA INSTALAÇÃO ARCH LINUX

### :no_entry: Tutorial apenas com o objetivo de deixar salvo o procedimento para instalação do Arch Linux via texto. :no_entry:
[![ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/Z8Z0HZOAZ) <br/>

## :green_circle: PRIMEIROS PASSOS 
:heavy_minus_sign: Baixar a ISO mais recente no site [Arch Linux](https://archlinux.org/download) <br/>
:heavy_minus_sign: Gravar a ISO no pendrive para tornar o mesmo bootável, pode-se utilizar o [Rufus](https://github.com/pbatard/rufus/releases/download/v3.21/rufus-3.21.exe) <br/>

## :green_circle: INSTALAÇÃO VIA MODO TEXTO
:heavy_minus_sign: Carregar a instalação do Arch Linux através do pendrive criado.

:heavy_minus_sign: Verificar se está com conexão à internet <br/>
```sh
ping www.google.com
```

:heavy_minus_sign: Caso esteja utilizando a conexão Wi-Fi, seguir o procedimento abaixo <br/>
 ```sh 
iwctl 
device list
station wlan0 (alterar para o nome da rede) scan
station wlan0 get-networks
station wlan0 connect "Nome da Rede"
digite a senha e valide com o ping
```

:heavy_minus_sign: Realizar o particionamento do disco (SSD / HD)
```sh 
cfdisk /dev/sda
Escolher o particionamento (GPT ou MBR)
Criar partição EFI (512mb) e alterar o tipo para EFI-System
Criar partição Swap (8GB vai depender da sua quantidade de ram) e alterar o tipo para Linux Swap
Criar partição raiz / (50GB) deixar o tipo como Linux Filesystem
Criar partição /home (espaço restante) deixar o tipo como Linux Filesystem
Clicar em Write (yes)
Quit
```

:heavy_minus_sign: Formatar a partição EFI
```sh
mkfs.fat -F32 /dev/sda1
```

:heavy_minus_sign: Formatar partição Swap
```sh
mkswap /dev/sda2
```
 
:heavy_minus_sign: Formatar a partição raiz e home
```sh
mkfs.btrfs /dev/sda3
mkfs.btrfs /dev/sda4
```

:heavy_minus_sign: Montar a partição raíz (/)
```sh
mount /dev/sda3 /mnt
```
		
:heavy_minus_sign: Criar a pasta /home e montar a mesma
```sh 
mkdir /mnt/home
mount /dev/sda4 /mnt/home
```

:heavy_minus_sign: Criar a pasta /boot e /boot/efi e montar as mesmas
```sh 
mkdir /mnt/boot
mount /dev/sda1 /mnt/boot
mkdir /mnt/boot/efi
mount /dev/sda1 /mnt/boot/efi
```

:heavy_minus_sign: Ativar a partição de SWAP
```sh 
swapon /dev/sda2
```	

:heavy_minus_sign: Editar a lista de mirrors para uma que esteja mais próxima de você
```sh 
nano /etc/pacman.d/mirrorlist
```
	
:heavy_minus_sign: Fazer o download da base do sistema
```sh 
pacstrap /mnt base base-devel linux linux-firmware nano dhcpcd
```		

:heavy_minus_sign: Gerar a tabela FSTAB
```sh 
genfstab -U -p /mnt >> /mnt/etc/fstab
cat /mnt/etc/fstab (validar o que foi gerado)
 ```		
 
:heavy_minus_sign: Entrar no sistema instalado
```sh 
arch-chroot /mnt
 ```	
 
## :green_circle: CONFIGURAÇÃO INICIAL APÓS INSTALAÇÃO DA BASE DO SISTEMA
:heavy_minus_sign: Fazer a configuração do localtime para sua localização
```sh 
ln -sf /usr/share/zoneinfo/America/Sao_Paulo /etc/localtime
 ```	
 
:heavy_minus_sign: Configurar o relógio com base no horário do sistema (BIOS)
```sh 
hwclock --systohc
```	

:heavy_minus_sign: Validar as configuraçãos do localtime e horário
```sh 
date
```	

:heavy_minus_sign: Alterar idioma do sistema para Português
```sh
nano /etc/locale.gen
descomentar as linhas pt_BR.UTF-8 UTF-8 e pt_BR ISO-8859-1
CTRL-O para salvar
CTRL-X para sair
```		

:heavy_minus_sign: Sincronizar as alterações realizadas para que seja gerado o locale.gen pt_BR
```sh
locale.gen
```

:heavy_minus_sign: Mapear as teclas ABNT2
```sh
echo KEYMAP-br-abnt2 >> /etc/vconsole.conf
```

:heavy_minus_sign: Alterar a senha do root
```sh
passwd
```

:heavy_minus_sign: Criar um novo usuário atribuindo os grupos wheel / storage / power e definir uma nova senha 
```sh
useradd -m -g users -G wheel,storage,power -s /bin/bash nomedousuário
passwd nomedousuário
```

:heavy_minus_sign: Instalação de pacotes úteis para o sistema iniciar corretamente
```sh
pacman -S dnfstools os-prober ntools network-manager-applet
```

:heavy_minus_sign: Realizar a configuração do boot (GRUB)
```sh
pacman -S grub efibootmgr
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=arch_grub --recheck
grub-mkconfig -o /boot/grub/grub.cfg
```

:heavy_minus_sign: Adicionar o usuário criado a lista de sudoers
```sh
su -
EDITOR=nano visudo
Descomentar a linha %wheel ALL=(ALL : ALL) ALL
CTRL-X
CTRL-O
```

:heavy_minus_sign: Instalar o servidor de imagens
```sh
sudo pacman -S xorg-server xorg-xinit xorg-apps mesa
```

:heavy_minus_sign: Instalar o driver de vídeo para o seu hardware (Intel / Nvidia / AMD), verificar o correto
```sh 
sudo pacman -S xf86-video-intel
sudo pacman -S nvidia nvida-settings
sudo pacman -S xf86-video-amdgpu
``` 	

:heavy_minus_sign: Instalar o ambiente gráfico e ativar o gerenciador de login (GNOME)
```sh 
sudo pacman -S gnome-extra gnome-terminal firefox gdm
systemctl enable gdm
``` 	
		
:heavy_minus_sign: Instalar o ambiente gráfico e ativar o gerenciador de login (KDE plasma)
```sh 
sudo pacman -S plasma konsole firefox sddm
systemctl enable sddm
```

:heavy_minus_sign: Ativar o Network Manager para a conexão DHCP entrar automaticamente
```sh 
systemctl enable NetworkManager
```

:heavy_minus_sign: Desligar ou reiniciar a máquina
```sh 
poweroff
reboot
```

### :bangbang: FEITO ISSO VOCÊ JÁ PODERÁ ENTRAR NO ARCH LINUX :bangbang:	
