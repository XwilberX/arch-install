# Guía de instalación de ArchLinux BIOS/UEFI (isipisi)

##  Descargar ArchLinux
Primero que todo tenemos que descargar la imagen ISO de arch, desde torrent o http que se encuentran en su [pagina oficial.](https://www.archlinux.org/download/)

## Requisitos

 - Arquitectura de x64 bits.
 - Imagen ISO de ArchLinux
 - Tener espacio disponible en disco duro en cado de ser dualboot o tener un disco duro vacío.
 - Conexión a internet
 - Bootear USB con la imagen ISO de ArchLinux
	 - Recomendación: BalenaEtcher 
## Pasos de la instalación
 - [ ] Arrancar desde el USB para iniciar el sistema de instalación  
 - [ ] Configuración de la distribución de teclado temporal (por defecto: US)  
 - [ ] Crear particiones al nuestro disco duro
 - [ ] Dar formatos a las particiones creadas
 - [ ] Montar particiones
 - [ ] Conexión a internet
 - [ ] Instalación de programas básicos (base)
 - [ ]  Entrar y configurar al sistema base
 - [ ] Desmontar particiones y reinicio de sistema
 - [ ] Configuraciones del sistema
 - [ ] Habilitar Network Manager
 - [ ] Actualizar sistema
 - [ ] Instalación de complementos gráficos
 - [ ]  Instalando un entorno grafico soportado oficialmente
 - [ ] Instalar gestor de inicio de sesión o Display Manager (D.M) 
 - [ ] Inicio manual de entorno de escritorio
 - [ ]  Fin de la instalación y reinicio

##  Arrancar desde el USB para iniciar el sistema de instalación 
Entramos a nuestra BIOS y arrancamos desde nuestro USB o unidad al cual tienes el sistema booteado.

Entramos con ENTER en la primera opción.

## Comienzo de la instalación 
Lo primero que veremos es una consola o promt de arch con usuario root con lo que haremos todas las configuraciones, tranqui da miedo un poquito, is isibro

## Configuración de distribución del teclado temporal 
Por defecto el sistema de instalación viene con el teclado en distribución ingles, si tienes un teclado con distribución en ingles no hagas nada, por otro lado si tienes un teclado en español tienes que hacer las siguientes configuraciones.

### Español España
Si eres de España ingresa esto:

    loadkeys es
   
   ### Español Latinoamérica
   Si eres de Latinoamérica ingresa esto   

    loadkeys la-latin1
## Crear particiones a nuestro disco duro
### Antes de particionar [IMPORTANTE]
Es importante saber si nuestro computador esta utilizando **BIOS** o si esta utilizando **UEFI**, asi mismo sabremos que tipo de particionado tendra.

Las tablas de partición **MBR** es utilizados por computadores con **BIOS** , por lo contrario **UEFI** lo típico es usar **GPT**.
### Identidicando BIOS/UEFI
Para saber que si nuestro computador utiliza BIOS o UEFI tenemos que hacer algo sencillo que es comprobar si un directorio existe, si existe es **UEFI** si no es **BIOS**, y con el siguiente comando podemos verificar eso:

    ls /sys/firmware/efi/efivars
Si aparece la consulta de este comando es que existe es **UEFI**, por lo contrario si no aparece o marca errores quiere decir que es **BIOS**
### Identificando discos
con el siguiente comando listar los discos que tenemos en nuestro computador:

    lsblk

***Es importante saber en que disco vamos a realizar las particiones ya que este re formateará y si tiene información o un SO se eliminara por completo***

**[RECOMENDACION]** Lo que suelo hacer para identificar cual es el disco que quiero es observar el tamaño de esto, es importante saber mínimo el tamaño de nuestro disco para no equivocarnos. 

 Otro comando que puede ser de ayuda es 

    fdisk -l
brinda mas información de los discos.

[OJO] Los discos suelen aparecer con unas etiquetas como "/dev/sda", "/dev/sdb", etc (también pueden tener numero). identifica bien esta etiqueta por que será de suma importancia en los siguientes pasos ya que asi es como son identificados.

### Ahora si a particionar.
Ya teniendo identificado nuestro disco, empezaremos con la tarea mas dificil o mas bien delicada de la instalacion de Arch.
Para esto podemos hacerlo por varios caminos lo mas fácil es usar cfdisk que es un programita con un "modo grafico" que no facilita esta tarea.

 Si tienes varios discos tienes que especificar cual de los discos es el que particionaras: 
 

    cfdisk /dev/sda
si tienes solo un disco o un disco vacío no tienes que especificar solo con llamar el comando identificara a ese por defecto

    cfdisk
Luego te aparecera un menu don de tendra que escoger segun el si tu PC usa  BIOS O UEFI: 

 - **BIOS** : usas **dos**
 - **UEFI**: usar **gpt**
### Particion boot (/boot)
La primera particion a crear es la de boot es en donde se almacena el Grub, el cargar de arranque.

El tamaño de la partición puede ser de 512 MB, ya que es no se necesita demasiado, la marcamos como ***Primary***.

Si estamos en **BIOS** notaremos que tenemos una opción de *Booteable*, le damos y tiene que tener un * en la parte de *Boot*.
Si en caso estamos en **UEFI** al crearla tenemos que cambiarla de tipo, le damos en ***Type*** y seleccionamos **EFI System**

Por ultimo le damos en ***write*** y escribimos ***yes*** para confirmar, esto es así en todas las particiones. 
### Partición root (/)
La partición root o del sistema es donde se guardaran los archivos del sistema, es decir la raíz. 

El tamaño es es cuestión de ti, como mínimo diría que unos 12 GB toma encuentra que aquí se instala el sistema. 
Elegimos el ***espacio vacío, new y primary***
### Partición home (/home)
La particion home es donde se guardaran tus archivos personales (doc, videos, imgs, etc).

El tamaño aquí igual depende de ti, solo toma encuentran eso que aquí estarán tus archivos personales.
Elegimos el ***espacio vacío, new y primary***
**[NOTA]** estas dos particiones se hacen igual en **BIOS** o en **UEFI**
### Partición de intercambio (SWAP)
La partición de intercambio o SWAP es como una ayuda a la memoria ram, es totalmente opcional pero muy recomendada-

El tamaño de esta partición debe ser el doble de tu memoria RAM.

Elegimos el ***espacio vacío, new y primary***
En esta partición tenemos que cambiarla de tipo, seleccionamos ***Type*** y escogemos *“**82 Linux swap / Solaris**”*.

**[OJOO]** seleccionaste write y confirmaste?, si no aun puedes hacerlo estas a tiempo.
##  Dar formatos a las particiones creadas
### Formato para partición /boot
Si estas en **UEFI**, el comando para formatear será: 

    mkfs.vfat -F32 /dev/sda1
   Si estas en **BIOS** será:

    mkfs.ext2 /dev/sda1
### Formato para partición root (/) y home (/home) en UEFI y BIOS

Root / el comando es:

    mkfs.ext4 /dev/sda2
  Home /home el comando es:
  

    mkfs.ext4 /dev/sda3
  ### Formato para el área de intercambio o SWAP
  Para dar formato utilizamos el siguiente comando:
  

    mkswap /dev/sda4
Para activar la particion ejecutamos el siguiente comando:

    swapon
## Montar particiones
### BIOS
Lo primero es montar la partición root o raíz, con el siguiente comando hacemos eso:

    mount /dev/sda2 /mnt
Crearemos los directorios dentro de /mnt para montar las particiones /boot y /home 

    mkdir /mnt/home
    mkdir /mnt/boot
Y ahora montamos las particiones en los directorios creado

    mount /dev/sda1 /mnt/boot
    mount /dev/sda3 /mnt/home
### UEFI
Montamos la particion root o raiz

    mount /dev/sda2 /mnt
Creamos los directorios dentro de /mnt para montar las particiones /boot/efi y /home 

    mkdir /mnt/home
    mkdir -p /mnt/boot/efi
Y ahora montamos las particiones en los directorios creados

    mount /dev/sda1 /mnt/boot/efi
    mount /dev/sda3 /mnt/home
## Conexión a internet
Verificamos si tenemos conexión a internet, con el siguiente comando:

    ping www.google.com
Si obtenemos respuesta es que si tenemos conexión a internet.
## Instalación de programas básicos (base)
Primero instalaremos el sistema base de Archlinux y algunos paquetes extra que utilizaremos luego de que el sistema base este instalado.
### Instalando Archlinux y paquetes necesarios
#### Para BIOS

    pacstrap /mnt base base-devel grub os-prober ntfs-3g networkmanager gvfs gvfs-afc gvfs-mtp xdg-user-dirs linux linux-firmware nano dhcpcd

#### Para UEFI 

    pacstrap /mnt base base-devel efibootmgr os-prober ntfs-3g networkmanager grub gvfs gvfs-afc gvfs-mtp xdg-user-dirs linux linux-firmware nano dhcpcd
  ### Instalación de paquetes adicionales

Si usarás el wifi, instala estos otros paquetes

`pacstrap /mnt netctl wpa_supplicant dialog`

En el caso que uses una laptop, el controlador para el touchpad

`pacstrap /mnt xf86-input-synaptics`
### Generar fstab
Ahora generaremos el archivo fstab, que es el que contiene la tabla de particiones del sistema.

    genfstab -pU /mnt >> /mnt/etc/fstab
   ## Entrar y configurar al sistema base
   Para entrar al sistema base instalado lo hacemos mediante este comando:
   

arch-chroot /mnt
indicando que la instalación esta montada en /mnt
### Creando Hostname

El hostname o nombre de equipo es el nombre con el que se reconocerá a tu equipo en la red, por eso es importante crearlo, ejecutamos el siguiente comando:

    echo nombredehost > /etc/hostname
Sustituye nombredehost por el nombre que quieras a tu PC

### Establecer la zona horaria
Para establecer la zona horaria tenemos que hacer un enlace simbólico con el archivo localtime, lo hacemos con el siguiente comando:

    ln -sf /usr/share/zoneinfo/_Región_/_Ciudad_ /etc/localtime
 #### Para México seria así:

    ln -sf /usr/share/zoneinfo/America/Mexico_City /etc/localtime
### Configuración de idioma del sistema
Para configurar el idioma tenemos que configurar el archivo locale.gen, para esop usaremos nano:

    nano /etc/locale.gen
Una vez dentro tenemos que descomentar nuestro pais, para eso borramos el #, en mi caso el de México es_MX.UTF-8.
Luego de eso, guardamos con Control+X y confirmamos para salir.

Generamos el archivo locale.gen con el siguiente comando

    locale-gen
### Configuración de el reloj de hardware

Cuando GNU/Linux arranca, el sistema está configurado para leer el reloj interno del equipo, después el reloj del sistema, que es independiente. Usaremos el comando hwclock -w para ajustar el reloj interno.

    hwclock -w
### Configurar distribución de teclado

Ahora, para configurar la distribución de nuestro teclado y dejarlo con el idioma que escogimos al principio de toda la instalación, cuando usamos el comando loadkeys (pero siempre tendremos que escoger nuestra distribución de teclado en las configuraciones de Teclado del entorno gráfico que escojas):

 ### Instalar Grub
  #### BIOS
    grub-install /dev/sda

 #### UEFI 

    grub-install --efi-directory=/boot/efi --bootloader-id='Arch Linux' --target=x86_64-efi
### Actualizar grub
Creamos el archivo grub.cfg, con el siguiente comando

    grub-mkconfig -o /boot/grub/grub.cfg
### Verificar sistemas operativos instalados
con el programa os-prober podemos verificar si tenemos otros sistemas operativos instalados y añadirlos en el grub, solo ejecutamos el siguiente comando:

    os-prober
Y después solo actualizamos el archivo de configuración de grub:

    grub-mkconfig -o /boot/grub/grub.cfg
### Establecer contraseña del admin (root)
Asi establecemos la pass para el root:

    passwd
y nos pedirá una contraseña la introducimos dos veces y listo.

### Creamos un nuevo usuarios que será el personal

comando para crear nuevo usuario:

    useradd -m myUser
colocamos contraseña: 

    passwd myUser
#### Otorgando permisos al usuario creado
Para otorgar permiso a nuestro usuario tenemos que editar el archivo de sudoers, nos apoyaremos de nano para eso:

    nano /etc/sudoers
Buscamos la linea ROOT ALL=(ALL) ALL, y de bajo agregamos nuestro usuario:

    myUser ALL=(ALL) ALL
   
### Reinicio

 1. Salimos de chroot `exit`
 2. Desmontamos particiones `umount -R /mnt` 
4. Reiniciamos `reboot`
5. inicio con usuario **root**
## Habilitar Network Manager
Es muy impórtate habilitar los servicios de networkmanger para tener acceso a internet, pata eso ejecutamos los siguientes comandos

    systemctl start NetworkManager.service
    systemctl enable NetworkManager.service
luego nos salimos con `exit`
### Primer login con usuario personal
 **Conexión a internet** 
 **WIFI**
 Si nos queremos conectar a una red wifi, ya no lo haremos por medio de wifi-menu, sino que con el siguiente comando:

`sudo nmcli dev wifi connect SSID password contraseña`

Donde  **SSID**  es el nombre de tu red y  **contraseña**  es la que tiene tu modem o la que le has puesto, si es de seguridad WPA2. Un ejemplo sería

`sudo nmcli dev wifi connect  **Turboneet**  password  **clarosv123**`

**CABLEADA**

Si usas un cable ethernet, pues no harás nada más que habilitar Network Manager, cosa que ya hicimos anteriormente cuando ejecutamos

`systemctl enable NetworkManager.service`
## Actualizar sistema
Actualizamos nuestro sistema con `sudo pacman -Syyu`
## Instalación de complementos gráficos
### Instalando el servidor X
para ellos ejecutamos:

    pacman  -S  xorg-server xorg-xinit xorg-server-utils
Y también para aceleración 3D instalamos mesa

    sudo pacman  -S  mesa mesa-demos
### Instalando drivers de video
Para **Intel**

    sudo pacman  -S  xf86-video-intel
Para **NVIDIA**
Hay varias alternativas, por un lado tenemos los driver **privativos**

    sudo pacman  -S  nvidia nvidia-utils nvidia-304xx

O podemos usar los de software libre, llamados  **Noveau**

    sudo pacman  -S  xf86-video-nouveau
Para **ATI(Radeon)**

    sudo pacman  -S  xf86-video-ati

En caso que no te funcione, tienes que instalar los drivers Vesa que son genéricos. El inconveniente es que no tiene soporte para

    sudo pacman  -S  xf86-video-vesa
## Instalando un entorno grafico soportado oficialmente
### Mate Desktop
`sudo pacman -S mate mate-extras`
### XFCE
`sudo pacman -S xfce4 xfce4-goodies network-manager-applet pulseaudio`
### LXDE
`sudo pacman -S lxde`
### KDE Plasma
`sudo pacman -S plasma kde-applications plasma-wayland-session`
### Gnome
`sudo pacman -S gnome gnome-extra`
### Cinnamon
`sudo pacman -S cinnamon`
### LXQT
`sudo pacman -S lxqt`
### Budgie Desktop
`sudo pacman -S budgie-desktop`
### Deepin desktop
`sudo pacman -S deepin deepin-extra`
## Instalar gestor de inicio de sesión o Display Manager (D.M)
### GDM
`sudo pacman -S gdm
sudo systemctl enable gdm.service`
### LightDM
`sudo pacman -S lightdm lightdm-gtk-greeter
sudo systemctl enable lightdm.service`
### LXDM
`sudo pacman -S lxdm
sudo systemctl enable lxdm.sercive`
### SDDM
`sudo pacman -S sddm
sudo systemctl enable sddm.service`
### XDM 
`sudo pacman -S xorg-xdm
sudo systemctl enable xorg-xdm.service`

## Inicio manual de entorno de escritorio
para que nuestro entorno se inicie después de instalarlo y reiniciar tenemos que crear un archivo para indicar eso, este archivo va en nuestro directorio de /home/myUser, lo haremos con los siguientes comandos 

    nano /home/myUser/.xinitrc
en el añadiremos una linea según el entorno:
### Mate-Desktop
`exec mate-session`
### XFCE
`exec startxfce4`
### LXDE
`exec startlxde`
### KDE-Plasma
`exec startkde`
### Gnome
`exec gnome-session`
### Cinnamon
`exec startlxqt`
### Deepin
`exec startdde`
## Fin de la instalación y reinicio
`reboot`