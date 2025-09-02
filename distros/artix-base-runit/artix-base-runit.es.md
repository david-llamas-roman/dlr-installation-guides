# 🤔 ¿Cómo Instalar Artix Base Runit?
- [🤔 ¿Cómo Instalar Artix Base Runit?](#-cómo-instalar-artix-base-runit)
  - [Introducción](#introducción)
  - [Descargar ISO](#descargar-iso)
    - [Problemas con la versión de 2025 de la ISO](#problemas-con-la-versión-de-2025-de-la-iso)
  - [Pantalla Inicial (antes del login)](#pantalla-inicial-antes-del-login)
  - [Login](#login)
  - [Particionado de la unidad de almacenamiento](#particionado-de-la-unidad-de-almacenamiento)
    - [¿Cómo sabemos si nuestra unidad de almacenamiento es /dev/sda?](#cómo-sabemos-si-nuestra-unidad-de-almacenamiento-es-devsda)
    - [¿Qué particiones crear y por qué?](#qué-particiones-crear-y-por-qué)
    - [Formatear particiones](#formatear-particiones)
    - [Montar particiones](#montar-particiones)
  - [Instalación del sistema base + linux-zen + runit](#instalación-del-sistema-base--linux-zen--runit)
  - [Fstab](#fstab)
  - [Chroot](#chroot)
  - [Configuración básica](#configuración-básica)
    - [Zona horaria](#zona-horaria)
    - [Locales](#locales)
    - [Hostname](#hostname)
    - [Crear contraseña para root](#crear-contraseña-para-root)
  - [Instalar rEFInd](#instalar-refind)
  - [Configurar red (runit)](#configurar-red-runit)
  - [Crear usuario y añadirlo al sudoers](#crear-usuario-y-añadirlo-al-sudoers)
  - [Salir, desmontar y reiniciar](#salir-desmontar-y-reiniciar)

## Introducción
¿Quieres instalar Artix Base Runit y no sabes por dónde empezar? Pues estás en el lugar indicado. Como habrás podido leer en el [README](../../README.es.md) del repositorio, si tenemos una guía de instalación de un sistema operativo, tenemos un tutorial en [YouTube](). Si entras y lo ves, podrás comprobar que hemos llevado a cabo la instalación de la distro anteriormente mencionada en una máquina virtual (utilizando [Oracle VirtualBox](https://www.virtualbox.org/)), eso no quita que tú no puedas instalarlo en un PC o portátil. La única diferencia es que antes tendrás que crear un USB booteable, para ello podrás usar programas como:
* [Rufus](https://rufus.ie/)
* [balenaEtcher](https://etcher.balena.io/)

## Descargar ISO
Para descargar la ISO, vamos a ir a la [sección de descargas de la web oficial de Artix](https://artixlinux.org/download.php). Acto seguido, nos desplazamos hacia abajo hasta encontrar la sección **Official ISO images** y la tenemos en **Stable ISO images** dentro de **base** (_artix-base-runit-[...]-x86_64.iso_).

### Problemas con la versión de 2025 de la ISO
Una vez tenía descargada la ISO y me disponía a instalar la distro, me topé con un problema, "Login incorrect". En esta distro, es necesario iniciar sesión con un usuario y contraseña que Artix nos aporta para continuar con la instalación, pues en la versión de 2025 de la ISO, da error con cualquier combinación de contraseña y password.
```bash
user = 'root' or 'artix'
password = 'artix'
```

Estuve buscando información sobre el error y sus posibles soluciones, pero, al parecer, no hay nada que hacer hasta que no lo arreglen. No sé exactamente si soy la única persona que se ha topado con dicho error, en caso de estar equivocándome y de existir una solución, ante todo pido disculpas y te animo a hacérmela llegar (nunca es tarde para aprender).

Entonces, ¿qué hacemos? ¿No instalamos la distro y ya está?

No, lo más sencillo en este caso es recurrir a una distro antigua de Artix Base Runit. En este caso, yo he llevado a cabo la instalación con la versión de 2024, en esta ISO no se presenta el problema que hemos explicado unas líneas atrás.

La ISO de 2024 se encuentra en [la misma página de descargas](https://artixlinux.org/download.php), pero en la sección **Old and archived ISO images**, concretamente, en **Old ISO images (click to expand)** dentro de **base**.

## Pantalla Inicial (antes del login)
<img src="./screenshots/screenshot-1.png" alt="Pantalla Inicial Artix Base Runit">

Aquí, lo que tenemos que hacer es ir cambiando el ***time zone (tz)***, el ***idioma del teclado (keytable)*** y el ***idioma de la distro (lang)*** en caso de no estar a nuestro gusto. Por ejemplo, yo el ***idioma de la distro (lang)*** lo he mantenido en inglés, sin embargo, he cambiado el ***time zone (tz)*** y el ***idioma del teclado (keytable)***.

>[!NOTE]
> En este menú inicial nos tenemos mover con el teclado.

Para salir y continuar con la instalación, tenemos que presionar ***From Stick/HDD: artix.x86_64***.

## Login
<img src="./screenshots/screenshot-2.png" alt="Artix Base Runit Login">

En Artix, tenemos un login inicial que debemos de hacer correctamente para poder empezar a introducir los comandos necesarios para completar la instalación de la distro.
```bash
user = 'root'
password = 'artix'
```

## Particionado de la unidad de almacenamiento
Para el particionado de la unidad de almacenamiento, vamos a utilizar una herramienta con una interfaz de tipo texto como es **cfdisk**. Para entrar a crear particiones en nuestra unidad, tenemos que introducir el siguiente comando:
```bash
cfdisk /dev/sda
```

### ¿Cómo sabemos si nuestra unidad de almacenamiento es /dev/sda?
Normalmente, Linux nombra las unidades de almacenamiento por orden de detección, por lo que, siempre que la unidad esté conectada mediante SATA/IDE/SCSI, va a ser **/dev/sda**.

Si se trata de un NVMe, si que puede ser algo distinto, **/dev/nvme0n1**. En cualquier caso, si queremos comprobarlo, podemos ejecutar comandos como:
```bash
lsblk
```
```bash
fdisk -l
```

### ¿Qué particiones crear y por qué?
| **Partición** | **Tamaño**       | **Función**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------- | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| EFI           | 512 MB           | Es para el arranque UEFI, lo que va a contener son cargadores de arranque. No necesita mucho espacio, con el espacio asignado debería ser suficiente, alguna gente le asigna más y otra gente menos.                                                                                                                                                                                                                                                                                                                                        |
| Swap          | 8 GB             | Área de intercambio de memoria, se utiliza para hibernar o por si faltase memoria RAM. Tradicionalmente se le ponían el doble de GB que los que teníamos instalados de RAM, pero, con la evolución del hardware y el aumento de GB en cuanto a memoria RAM, podemos no cumplir dicha regla. El único problema que nos encontraríamos si quisiéramos asignar menos espacio del que tenemos en memoria RAM es que no podríamos hibernar, ya que para llevar a cabo dicha tarea necesitamos como mínimo los mismos GB que tenemos de RAM.      |
|               |                  | Ejemplo: Si tenemos 2gb de RAM, lo lógico sería poner el doble (4). Si tenemos 4gb de RAM, repetimos, aunque en ambos casos podemos poner más del doble para ir sobrados. Si tenemos 32gb RAM, podríamos poner de swap entre 8-16gb (si no queremos hibernación) y de 32 en adelante si sí queremos dicha característica.                                                                                                                                                                                                                   |
|               |                  | Digamos que las cifras de swap que hoy en día se pueden manejar son entre 4 y 16gb (sin hibernación) y, con hibernación, los GB que tenemos de RAM en adelante.                                                                                                                                                                                                                                                                                                                                                                             |
| / (root)      | 30 GB            | Es la partición raíz (root), aquí tenemos todo el sistema operativo y las aplicaciones. En este caso, los valores recomendados de espacio serían entre los 20 y 50gb siendo 30gb suficiente.      Ojo, que sea popular una serie de números de GB en esta partición, no significa que no podammos asignar más o menos GB.                                                                                                                                                                                                                   |
| /home         | espacio restante | Contiene todos tus archivos personales.                                                                                                                                                                                                                                                                                                                          Podríamos tener estos archivos dentro de / (root) y eliminar /home, pero lo que ganamos con esta configuración es la posibilidad de reinstalar Linux sin perder tus datos. |

### Formatear particiones

| Partición | Formato | Motivo                                                                                                                                                                                                                                                      |
| --------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| EFI       | FAT32   | El estándar UEFI únicamente arranca desde particiones con dicho formato.                                                                                                                                                                                    |
| Swap      |         | Esta partición es algo particular, ya que "swap" no es un sistema de archivos. Simplemente es un espacio en la unidad de almacenamiento que el kernel utiliza como extensión de la RAM. Entonces, lo que hacemos realmente no es formatear, es inicializar. |
| / (root)  | ext4    | Para esta partición es necesario un sistema de archivos robusto que tenga una serie de características y, en Linux, el estándar es dicho formato.                                                                                                           |
| /home     | ext4    | En esta partición, pasa lo mismo que en la partición raíz (/).                                                                                                                                                                                              |

Una vez que ya sabemos los formatos a los que tenemos que formatear cada partición, vamos a ejecutar los siguientes comandos:
* **EFI - FAT32**
  ```bash
  mkfs.fat -F32 /dev/sda1
  ```
* **Swap**
  ```bash
  mkswap /dev/sda2
  ```
* **/ (root) - EXT4**
  ```bash
  mkfs.ext4 /dev/sda3
  ```
* **/home - EXT4**
  ```bash
  mkfs.ext4 /dev/sda4
  ```

### Montar particiones
Cuando ya tenemos las particiones creadas y formateadas, necesitamos montarlas. Montar cada partición es necesario para que el sistema las utilice, ya que, Linux, organiza todo dentro de un árbol único de directorios que empiezan en ***/*** y, por defecto, las particiones no vienen asignadas a un elemento específico del árbol, eso lo tenemos que hacer nosotros.

1. Para montar la raíz, hacemos lo siguiente:
   ```bash
   mount /dev/sda3 /mnt
   ```
   Aquí, ***/mnt*** actúa como raíz provisional del nuevo sistema.

2. Para montar EFI en ***/boot***, hacemos lo siguiente:
   ```bash
   mkdir /mnt/boot
   ```
   ```bash
   mount /dev/sda1 /mnt/boot
   ```
   Gracias a esto, el bootloader se instalará ahí.

3. Para montar ***/home***, hacemos lo siguiente:
   ```bash
   mkdir /mnt/home
   ```
   ```bash
   mount /dev/sda4 /mnt/home
   ```
   Gracias a esto, los datos del usuario quedarán en su propia partición.

4. Para activar la ***/swap***, hacemos lo siguiente:
   ```bash
   swapon /dev/sda2
   ```
   Con esto, hacemos que la partición swap esté lista para cuando se necesite.

## Instalación del sistema base + linux-zen + runit
Ahora, vamos a instalar todo lo mínimo e indispensable para nuestra distro. Para ello, vamos a utilizar **basestrap** que es el encargado de copiar los paquetes seleccionados en la partición raíz que acabamos de montar (/mnt).

A continuación, vamos a ver el comando completo y vamos a ir viendo poco a poco qué es cada uno de los paquetes que estamos instalando.
```bash
basestrap /mnt base base-devel linux-zen linux-zen-headers linux-firmware \
  runit elogind-runit dhcpcd-runit sudo
```
* **base**

  Conjunto mínimo de paquetes para tener un sistema Arch (o distros basadas en Arch) funcional (shell, utilidades básicas, núcleo del sistema...).

* **base-devel**

  Herramientas de compilación (gcc, make...). Útil para instalaciones que requieran de compilación.

* **linux-zen**

  Versión del kernel de Linux modificada para mejorar la capacidad de respuesta y la latencia en entornos de escritorio, juegos y multimedia, priorizando la interacción fluida del usuario.

* **linux-zen-headers**

  Cabeceras del kernel necesarias para instalar drivers o módulos externos.

* **linux-firmware**

  Firmware para tarjetas gráficas, Wi-Fi, Bluetooth... Sin esto, hay hardware que no funciona.

* **runit**

  Sistema de inicialización alternativo a systemd. Es ligero, rápido y sencillo.

* **elogind-runit**

  Implementación de login/session manager para runit (sustituto de logind de systemd). Necesario para que sesiones gráficas y permisos de usuarios funcionen bien.

* **dhcpcd-runit**

  Cliente DHCP + integración con runit, necesario para tener red automática (IP por DHCP).

* **sudo**

  Permite ejecutar comandos como root desde un usuario normal con permisos. Imprescindible para administrar el sistema sin tener que iniciar sesión como root.

## Fstab
Una vez llegados a este punto, tenemos que lanzar el siguiente comando. Comando, que nos servirá para definir cómo se van a montar las particiones automáticamente cada vez que se arranque el sistema.
```bash
fstabgen -U /mnt >> /mnt/etc/fstab
```
> [!NOTE]
> **fstab** es un archivo de configuración de Linux donde se especifica qué particiones usar, dónde montarlas, con qué sistema de archivos y demás.

## Chroot
Chroot significa "change root", en español, cambiar raíz y lo que hace es hacer que un directorio actúe como la raíz del nuevo sistema que estamos instalando sin necesidad de reiniciar.

En el caso de Artix, el comando es ***artix-chroot***, que es una versión del chroot original preparada para dicha distro. Lo que hace por detrás el comando anteriormente mencionado es lo siguiente:
1. Entra al nuevo sistema (/mnt).
2. Monta y transfiere cosas necesarias del entorno actual (como /proc, /sys, /dev...) para que el nuevo sistema funcione como si ya estuviera arrancado. 

En definitiva, lo que hace es que la raíz provisional (/mnt) se convierta en la raíz real del sistema.

```bash
artix-chroot /mnt
```

## Configuración básica
### Zona horaria
```bash
ln -sf /usr/share/zoneinfo/Europe/Madrid /etc/localtime
```
Este comando enlaza tu zona horaria (en este caso, estamos en España, por tanto, escogemos Europe/Madrid) con el directoria ***/etc/localtime***, que es el directorio que Linux utiliza para saber la hora correcta.

```bash
hwclock --systohc
```
Este comando sincronizamos el reloj de hardware (BIOS/UEFI) con la hora del sistema.

### Locales
```bash
echo "es_ES.UTF-8 UTF-8" >> /etc/locale.gen
```
De esta manera le decimos al sistema qué idioma soportar, en este caso, el idioma seleccionado es el español, podría ser otro cualquiera (inglés de Reino Unido, "en_GB.UTF-8 UTF-8" o de EE.UU. "en_US.UTF-8 UTF-8").

```bash
locale-gen
```
Este comando genera los archivos de idioma necesarios.

```bash
echo LANG=es_ES.UTF-8 > /etc/locale.conf
```
De esta manera definimos el idioma principal del sistema.

### Hostname
```bash
echo artixvbox > /etc/hostname
```
De esta manera definimos el nombre del ordenador dentro de la red.

```bash
cat >> /etc/hosts <<EOF
127.0.0.1 localhost
::1       localhost
127.0.1.1 artixvbox.localdomain artixvbox
EOF
```
De esta manera enlazamos el nombre con la IP local para que todo funcione en red interna y servicios.

### Crear contraseña para root
```bash
passwd
```
Este comando sirve para crear una contraseña a un usuario en específico, en este caso que no escribimos ningún nombre a la derecha del mismo, al root.

## Instalar rEFInd
```bash
pacman -S refind efibootmgr
```
Con este comando, estamos instalando el gestor de arranque rEFInd y la herramienta para manejar entradas UEFI.

```bash
refind-install
```
Este comando sirve para copiar los archivos de rEFInd a la partición EFI y lo registra en el firmware para que el ordenador pueda arrancar.

## Configurar red (runit)
```bash
mkdir -p /run/runit/service
```
```bash
ln -s /etc/runit/sv/dhcpcd /run/runit/service
```
Con estos 2 comandos, activamos el servicio de red dhcpcd en runit para, que al arrancar el sistema, se pida IP automáticamente y se tenga internet.

## Crear usuario y añadirlo al sudoers
```bash
useradd -m -G wheel -s /bin/bash username
```
```bash
passwd username
```
Con estos 2 comandos creamos un usuario normal y lo añadimos al grupo wheel (admins), también le asignamos bash como shell y le creamos una contraseña.

```bash
pacman -S nano
```
En el caso de que ya tengamos nano instalado, podemos omitir este comando, pero, si no, tenemos que ejecutarlo para instalar el editor anteriormente mencionado.

```bash
%wheel ALL=(ALL) ALL
```
Acto seguido, abrimos el archivo ***/etc/sudoers*** con nano y buscamos la línea que has visto justo arriba. Una vez la encontremos, la descomentamos. De esta manera, cualquier usuario del grupo wheel, podrá utilizar sudo. 

## Salir, desmontar y reiniciar
```bash
exit
```
De esta manera, salimos del chroot y volvemos al entorno de la ISO.

```bash
umount -R /mnt
```
Con el comando anterior desmontamos todas las particiones de la unidad de almacenamiento.

```bash
swapoff -a
```
De esta manera, desactivamos el swap que activamos en pasos anteriores.

```bash
reboot
```
Por último, reiniciamos.