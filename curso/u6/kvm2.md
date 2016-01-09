---
layout: blog
tittle: Curso OpenStack
menu:
  - Unidades
---

##Introducción a KVM

<a href="http://www.linux-kvm.org/page/Main_Page">Kernel Based Virtual Machine
(KVM)</a> es una solución de virtualización completa para linux que funciona
sobre procesadores x86 con extensiones de virtualización por hardware. El
componente principal de kvm es el módulo kvm.ko que fue incluido en el *kernel*
linux 2.6.20.

KVM no proporciona una solución completa de virtualización, sino que reutiliza
muchos componentes del proyecto de emulación y virtualización <a
href="http://wiki.qemu.org/Main_Page">qemu</a>.

Se puede utilizar KVM directamente o a través de la API de virtualización <a
href="http://libvirt.org/">libvirt</a>, como en el caso de OpenStack.

###Instalación de KVM

Para la ejecución de KVM se necesita de un procesador con las extensiones de virtualización x86. Se puede comprobar esto a través del comando:

	# egrep -c '(vmx|svm)' /proc/cpuinfo
    
Según el resultado:
* 0: no hay soporte para KVM.
* 1 ó más: hay soporte de virtualización para KVM. Pero hay que comprobar también que esté activo en BIOS.

Una vez comrado si tenemos acelaración hardware, instalamos los siguientes paquetes básicos:
    
	# apt-get install qemu-kvm libvirt-bin bridge-utils
    
  * **qemu-kvm**: sistema KVM. Incluyendo el módulo del kernel.
  * **libvirt-bin**: toolkit C para la interacción con el hipervisor.
  * **bridge-utils**: herramientas para la configuración de bridges Ethernet.

Los usuarios que vayan a gestionar las máquinas virtuales deben pertenecer a los grupos: *libvirtd* y *kvm*.

Como software adicional se puede instalar:

	# apt-get install virtinst virt-manager ubuntu-vm-builder virt-viewer
    
  * **virtinst**: proporciona herramientas en línea de comandos para la creación y clonado de VMs.
  * **virt-manager**: interfaz gráfica para la gestióon de VMs.
  * **ubuntu-vm-builder**: scripts para la automatización de la creación de VMs basadas en Ubuntu.
  * **virt-viewer**: permite la conexión a la consola de la MV a través del protocolo VNC.

Podemos asegurarnos de que todo funciona correctamente a través de:

	# virsh -c qemu:///system list
	Id Nombre          Estado
	----------------------------------

###Almacenamiento de las máquinas virtuales

Para la configuraci ́on inicial de KVM hay que crear un pool de almacenamiento para guardar las im ́agenes de las VMs. Seguimos los siguientes pasos:

1. Editamos el fichero /tmp/pool-default.xml con el siguiente contenido:

		<pool type=’dir’>
			<name>default</name>
			<target>
				<path>/var/lib/libvirt/images</path>
			</target>
		</pool>

2. Definimos el pool en libvirt:

		# virsh pool-define /tmp/pool-default.xml

3. Lo iniciamos:

		# virsh pool-start default

4. Lo configuramos para que si inicie siempre de forma automática:

		# virsh pool-autostart default

5. Comprobamos:

		# virsh pool-list --all
		Nombre 			Estado 			Inicio automático
		-----------------------------------------
		default 		activo 			si

6. El fichero de creaci ́on del pool se encuentra en: */etc/libvirt/storage/default.xml*

Por defecto se utiliza un pool de almacenamiento basado en directorio, por defecto es */var/lib/libvirt/images*. Este directorio puede ser una partición aparte, un volumen lógico
LVM o cualquier otro tipo de almacenamiento. Se pueden crear mucho más pools y de diferentes tipos.

### Introducción a las redes en KVM

Para la configuración de la red seguimos los siguientes pasos:

1. Iniciamos la red por defecto:

		# virsh net-start default

2. Configuramos para que se inicie siempre de forma automática:

		# virsh net-autostart default

3. Listamos la configuración de red disponible.

		# virsh net-list
		Nombre 		Estado 		Inicio automático
		-----------------------------------------
		default 	activo 		si

Las características de la red por defecto (default):

* La VMs reciben una IP por DHCP.
* El anfitrión hace NAT para la conexión de los invitados.
* El fichero de configuración se encuentra en: */etc/libvirt/qemu/networks/default.xml*

El contenido del fichero es:

	<network>
		<name>default</name>
		<bridge name="virbr0" />
		<forward/>
		<ip address="192.168.122.1" netmask="255.255.255.0">
			<dhcp>
				<range start="192.168.122.2" end="192.168.122.254" />
			</dhcp>
		</ip>
	</network>

### Servicio libvirt

Siempre que hagamos cualquier cambio de configuración podemos reiniciar el sistema libvirt a través de:

	# service libvirt-bin restart

También podemos gestionar el servicio con:

	# service libvirt-bin start
	# service libvirt-bin stop
	# service libvirt-bin status

###Creación de máuinas virtuales

Hay varias utilidades que permiten la creación de máquinas virtuales sobre KVM. Destacamos las siguientes:
* Comando **virt-install**.
* Comando **vmbuilder** (antes ubuntu-vm-builder).
* Aplicación gráfica **virt-manager**.
* Directamente usando QEMU/KVM (comando **qemu/kvm**).

Vamos a estudiar la herramienta **virt-install**. virt-install es un comando que permite el aprovisionamiento de nuevas máquinas virtuales, es una herramienta en línea de comandos que permite la creación de máquinas virtuales Xen y KVM utilizando libvirt.

Como características destacamos:
* Está  basada en libvirt, por lo que puede trabajar sobre varios hipervisores.
* Permite la instalación de SSOO tanto en modo texto (consola serie) como en modo gráfico (VNC ó SDL).
* Se pueden crear VM con varios discos, varias interfaces de red, dispositivos de audio, dispositivos USB ó PCI, entre otros.
* Soporta varios métodos de instalación:
	* Locales: discos duros, ficheros, unidad CD/DVD, ...
	* Remotos: NFS, HTTP, FTP, ...
	* Por red: PXE.
	* A partir de imágenes existentes.

Básicamente hay que indicar:
* Nombre de la VM.
* Cantidad de RAM.
* Almacenamiento.
* Método de instalación.

Por lo que las opciones obligatorias son son: *--name, --ram, --disk*; más las relativas a la instalación como *--cdrom, --location, ...*

**Ejemplo: instalar Ubuntu 12.04 desde una imagen ISO**

	virt-install --connect qemu:///system
				--virt-type=kvm
				--name VirtualMachine01
				--ram 1024
				--vcpus=2
				--disk path=/var/lib/libvirt/images/VirtualMachine01.img,size=8
				--cdrom /var/lib/libvirt/images/ubuntu-12.04-server-amd64.iso
				--os-type linux
				--os-variant=ubuntuprecise
				--graphics vnc,keymap=es
				--noautoconsole
				--network network=default
				--description "Ubuntu 12.04 Server"

