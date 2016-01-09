---
layout: blog
tittle: Curso OpenStack
menu:
  - Unidades
---

##Intreoducción a KVM

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
