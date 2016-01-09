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

Nos podemos conectar a la consola para ver el proceso de instalación a travñes del protocolo VNC, de varias formas:

	# virt-viewer -c qemu:///system VirtualMachine01
	# vinagre localhost:0
	# vncviewer localhost:0

###Gestión de máquinas virtuales

Se utilice la herramienta que se utilice, la configuración de la máquina virtual reside en un fichero XML como éste:

	<domain type=’kvm’>
		<name>Ubuntu01</name>
		<uuid>18dbd172-97f7-242f-ebc8-c8b377b8ec71</uuid>
		<description>Ubuntu 12.04 Server</description>
		<memory>524288</memory>
		<currentMemory>524288</currentMemory>
		<vcpu>2</vcpu>
		<os>
			<type arch=’x86_64’ machine=’pc-1.0’>hvm</type>
			<boot dev=’hd’/>
		</os>
		<features>
			<acpi/>
			<apic/>
			<pae/>
		</features>
		<clock offset=’utc’/>
		<on_poweroff>destroy</on_poweroff>
		<on_reboot>restart</on_reboot>
		<on_crash>restart</on_crash>
		<devices>
			<emulator>/usr/bin/kvm</emulator>
			<disk type=’file’ device=’disk’>
				<driver name=’qemu’ type=’raw’/>
				<source file=’/var/VirtualMachines/libvirt/Ubuntu01.img’/>
				<target dev=’vda’ bus=’virtio’/>
				<address type=’pci’ domain=’0x0000’ bus=’0x00’ slot=’0x04’ function=’0x0’/>
			</disk>
			<disk type=’block’ device=’cdrom’>
				<driver name=’qemu’ type=’raw’/>
				<target dev=’hdc’ bus=’ide’/>
				<readonly/>
				<address type=’drive’ controller=’0’ bus=’1’ unit=’0’/>
			</disk>
			<controller type=’ide’ index=’0’>
				<address type=’pci’ domain=’0x0000’ bus=’0x00’ slot=’0x01’ function=’0x1’/>
			</controller>
			<interface type=’network’>
				<mac address=’52:54:00:75:cc:68’/>
				<source network=’default’/>
				<model type=’virtio’/>
				<address type=’pci’ domain=’0x0000’ bus=’0x00’ slot=’0x03’ function=’0x0’/>
			</interface>
			<serial type=’pty’>
				<target port=’0’/>
			</serial>
			<console type=’pty’>
				<target type=’serial’ port=’0’/>
			</console>
			<input type=’mouse’ bus=’ps2’/>
			<graphics type=’vnc’ port=’-1’ autoport=’yes’ keymap=’es’/>
			<video>
				<model type=’cirrus’ vram=’9216’ heads=’1’/>
				<address type=’pci’ domain=’0x0000’ bus=’0x00’ slot=’0x02’ function=’0x0’/>
			</video>
			<memballoon model=’virtio’>
				<address type=’pci’ domain=’0x0000’ bus=’0x00’ slot=’0x05’ function=’0x0’/>
			</memballoon>
		</devices>
	</domain>

Para la gestión de las máquinas virtuales se cuenta con dos herramientas:

* **virsh**: herramienta en modo comando.
* **virt-manager**: herramienta gráfica.

**virsh** es la principal herramienta para la gestión completa de KVM, esta basada en libvirt. Permite iniciar,pausar y apagar máquinas virtuales. Pero también
permite otras funciones como gestión de la red, de los pool de almacenamiento, de los volúmenes, de las snapshots, etc. Presenta dos modos de funcionamiento, modo comando y modo
interactivo.

Para conectarnos al hipervisor podemos optar por:

* Conectarnos en local:

	# virsh
	# virsh -c qemu:///system

* Conectarnos a un hipervisor remoto:

	# virsh -c qemu+ssh://usuario@host:port/system

Entre los comandos genéricos más importantes de virsh destacamos:

* **help** Muestra la ayuda.
* **exit** Sale del intérprete.
* **connect** Se (re)conecta al hipervisor.
* **uri** Muestra la URI del hipervisor al que estamos conectados.
* **hostname** Muestra el hostname del hipervisor.
* **list** Muestra un listado de las VMs que están en ejecución.
* **list --all** Muestra un listado de todas las VMs.
* **console** Conecta a la consola virtual de una VM.
* **create** Crea un VM desde su fichero de configuraci ́on XML.
* **dumpxml** Muestra la configuración de la VM en XML.
* **edit** Permite editar la configuración XML de una VM.
* **dominfo** Muestra la información básica de una VM
* **migrate** Permite migrar una VM de un anfitrión a otro.
* **start** Inicia una VM.
* **autostart** Permite configurar una VM para que se inicie de forma automática durante el inicio del anfitrión.
* **shutdown** Apaga la VM de forma ordenada.
* **destroy** Termina inmediatamente una VM, apagado hardware.
* **reboot** Reinicia una VM.
* **reset** Reinicia inmediatamente una VM.
* **suspend**
* **resume** Suspende/reinicia una VM. Una VM suspendida sigue en memoria pero no entra en la planificación del hipervisor.