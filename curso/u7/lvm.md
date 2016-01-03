---
layout: blog
tittle: Curso OpenStack
menu:
  - Unidades
---

###Introducción a LVM

lvm2 es la implementación de Logical Volume Manager para el kernel de linux y es un sistema que permite almacenar sistemas de ficheros de una manera mucho más flexible que las clásicas particiones de un disco.

##Instalando los paquetes necesarios

En un equipo con Debian, bastará con hacer:

	apt-get install lvm2

##Definiendo los volúmenes físicos (PV)

Podemos añadir diferentes particiones o discos que contendrán los volúmenes lógicos, por ejemplo:

	pvcreate /dev/hda4
	pvcreate /dev/sdb2
	pvcreate /dev/hdc

##Creando el grupo de volúmenes (VG)

Con los volúmenes físicos que se desee (puede ser sólo uno) se forma un grupo de volúmenes:

	vgcreate vg1 /dev/hda /dev/hdb

Donde se ha creado el grupo de volúmenes vg1 con los volúmenes físicos /dev/hda y /dev/hdb, siendo este paso "similar" a la configuración de RAID0.

##Creando un volumen lógico (LV)

El volumen lógico va a ser el espacio que contendrá el sistema de ficheros y por tanto el equivalente a una partición tradicional, para crearlos basta con hacer:

	lvcreate --size 1g -n ejem vg1

donde se creará un volumen lógico de 1 GiB, de nombre ejem y perteneciente al grupo de volúmenes vg1. El dispositivo asociado a este volumen lógico será:

	/dev/vg1/ejem -> /dev/mapper/vg1-ejem

y posteriormente habrá que formatearlo, montarlo, etc. igual que si se tratase de una partición física de un disco.

	fdisk /dev/mapper/vg1-ejem
	mkfs.ext4 /dev/mapper/vg1-ejem
	mount /dev/mapper/vg1-ejem /mnt

##Snapshot de volúmenes

Se puede entender los snapshot o instantáneas de volúmenes como una "foto" de un volumen lógico. Los snapshot sólo almacenan los cambios con respecto al volumen original.

Vamos crear un snapshot donde se pueda almacer hasta 512 MB de cambios:

	lvcreate -L512m -s -n ejem_snapshot /dev/vg1/ejem

Y comprobamos que se ha creado:

	lvs
	  LV            VG   Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
	  ejem          vg1  owi-aos---   1.00g                                                    
	  ejem_snapshot vg1  swi-a-s--- 512.00m      ejem   0.00 

A continuación vamos a hacer un cambio en el volumen (vamos a crear un fichero de 100MB) y original y vemos cómo ha variado el porcentaje de cambios e el snapshot:

	/mnt# dd if=/dev/zero of=disco-virtual.img bs=512 count=209715
	209715+0 records in
	209715+0 records out
	107374080 bytes (107 MB) copied, 0.864276 s, 124 MB/s

Y comprobamos los cambios:

	lvs
	 LV            VG   Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
	 ejem          vg1  owi-aos---   1.00g                                                    
	 ejem_snapshot vg1  swi-a-s--- 512.00m      ejem   20.09                                  


##Aumentar el tamaño de un volumen lógico ya creado

Supongamos que tenemos un volumen lógico creado, formateado y montado y que queremos aumentar su tamaño porque se nos queda pequeño. Aunque sea obvio, hay que decir que se podrá aumentar el tamaño del volumen lógico siempre que tengamos espacio libre en el grupo de volúmenes :-).

Supongamos que tenemos el volumen anterior de 1GiB y queremos añadirle 10 GiB más, basta con hacer:

	lvresize -L+10G /dev/mapper/vg1-ejem

(También se puede utilizar lvextend con la misma sintaxis)

Ahora bien, el sistema de ficheros sigue teniendo el tamaño anterior, por lo que habrá que redimensionarlo utilizando resize2fs (suponiendo que se trata de un sistema de ficheros ext4)

	resize2fs /dev/mapper/vg1-ejem 11G

que debe hacerse con el volumen desmontado, para evitar la posibilidad de corrupción de datos.