---
layout: blog
tittle: Curso OpenStack
menu:
  - Unidades
---

##Formato de imágenes

Al crear una máquina virtual debemos crear uno o varios discos virtuales, existen muchos tipos de formatos de imagen de disco, destacan:

* **raw**: Formato de imagen de disco en crudo (no estructurado). Es el formato b ́asico que puede crearse con herramientas como dd. No está optimizado para su utilizaci ́on en virtualización, pero puede manejarse con herramientas básicas del sistema (dd, parted, fdisk, mount, kpartx, etc.). Muy portable entre hipervisores.
* **vhd**: Usado por herramientas de virtualización como VMware, Xen, VirtualBox y otros.
* **vmdk**: Formato utilizado por VMware en productos como VMware Workstation.
* **vdi**: Formato soportado por VirtualBox.
* **iso**: Volcado del sistema de ficheros contenido en un CD/DVD.
* **qcow2**: Formato de imagen soportado por QEMU con características avanzadas como expansión dinámica, copy-on-write, snapshots, cifrado, compresión, etc.
* **vpc**: Formato de imagen de Virtual PC.
* **ami**: Formato Amazon Machine Image.

Los formatos soportados por QEMU/KVM son: raw, cow, qcow, qcow2, vmdk y vdi.

Para la gestión de imágenes virtuales de disco se puede utilizar el comando *qemu-img* (paquete qemu-utils). Este comando permite:

* Chequear una imagen de disco.
* Realizar conversiones entre formatos de imagen.
* Proporcionar información de la imagen.
* Gestionar las snapshots contenidas en una imagen.
* Redimensionar una imagen.

Los formatos soportados son: raw, qcow2, qcow, cow, vdi, vmdk, vpc y cloop.
Al crear una imagen con *virt-install* podemos especificar el formato a través de la opción format del parámetro disk: Ejemplo:

	--disk pool=default,size=4,format=qcow2