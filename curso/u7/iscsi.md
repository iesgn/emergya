---
layout: blog
tittle: Curso OpenStack
menu:
  - Unidades
---

###Introducción a iSCSI

iSCSI es un estándar que permite utilizar la interfaz de transferencia de datos
de bus SCSI a través de una red TCP/IP. Es un protocolo de la capa de transporte
que básicamente permite la utilización de un dispositivo de bloques en un equipo
remoto. 

###Conceptos previos

iSCSI tiene una arquitectura tipo cliente servidor; las partes que la conforman son las siguientes:

* **target iSCSI**: El target iSCSI es el servidor. Un target puede ofrecer uno o más recursos iSCSI por la red. En las soluciones Linux para iSCSI, no hace falta que el dispositivo a exportar sea necesariamente un disco SCSI; se pueden exportar cualquier dispositivos de bloque como por ejemplo: Particiones RAID, volúmenes LVM, discos físicos,... Para esta documentación vamos a utilizar el target iSCSI tgt.
* **iniciador iSCSI**: El iniciador es el cliente de
iSCSI. Generalmente el iniciador consta de dos partes: los módulos  o
drivers que proveen soporte para que el sistema operativo pueda
reconocer discos de tipo iSCSI y una aplicación de espacio de
usuario que gestiona las conexiones a dichos discos. Como iniciador
vamos a utilizar la aplicación open-iscsi. 

###Instalación y configuración del target iSCSI tgt

En debian vamos a instalar el servidor con la siguiente instrucción:

	apt-get install tgt

Tenemos un volumen de 512MiB que vamos a exportar con iSCSI:

	lvs
	  LV   VG   Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
	  ejem vg1  -wi-a----- 512.00m      

Para ello vamos a configurar tgt para crear un nuevo target iSCSCI, para ello añadimos en el fichero /etc/tgt/targets.conf, la siguiente definición:

	<target iqn.2016-01-com.example.tg1>
	    backing-store /dev/vg1/ejem
	</target>

Y reiniciamos el servicio:

	systemctl restart tgt

Y comprobamos que se ha creado el nuevo target iSCSI asociado a nuestro volumen lógico, con la siguiente instrucción:

	tgtadm --mode target --op show
	Target 1: iqn.2016-01-com.example.tg1
	    System information:
	        Driver: iscsi
	        State: ready
	    I_T nexus information:
	    LUN information:
	        LUN: 0
	            Type: controller
	            SCSI ID: IET     00010000
	            SCSI SN: beaf10
	            Size: 0 MB, Block size: 1
	            Online: Yes
	            Removable media: No
	            Prevent removal: No
	            Readonly: No
	            SWP: No
	            Thin-provisioning: No
	            Backing store type: null
	            Backing store path: None
	            Backing store flags: 
	        LUN: 1
	            Type: disk
	            SCSI ID: IET     00010001
	            SCSI SN: beaf11
	            Size: 537 MB, Block size: 512
	            Online: Yes
	            Removable media: No
	            Prevent removal: No
	            Readonly: No
	            SWP: No
	            Thin-provisioning: No
	            Backing store type: rdwr
	            Backing store path: /dev/vg1/ejem
	            Backing store flags: 
	    Account information:
	    ACL information:
	        ALL

###Instalación del cliente open-iscsi

En la máquina que va a funcionar como cliente, instalamos el iniciador iscsi open-iscsi:

	 apt-get install open-iscsi

La configuración de open-iscsi se realiza a través del comando iscsiadm y la misma se guarda en una base de datos. Si queremos cambiar algún parámetro de la configuración tenemos que hacerlo a través de iscsiadm.

####Detección del target

En primer lugar tenemos que indicarle a iscsiadm que detecte nuestro target iSCSI y lo agregue a su base de datos. Hay que aclarar que iscsiadm tiene tres formas de operación

* discovery: En este modo se pueden descubrir targets y agregarlos a la base de datos.
* node: En este modo se administran los targets ya descubiertos y se pueden visualizar datos acerca de estos nodos, así como conectarse a ellos.
* session: En este modo se administran los targets a los que se está conectados (en los que se ha hecho login).


Para descubrir nuestro target usamos obviamente "discovery":

	iscsiadm -m discovery -t sendtargets -p 192.168.100.1
	192.168.100.1:3260,1 iqn.2016-01-com.example.tg1

A continuación nos podemos conectar al target utilizando la siguiente instrucción:	

	iscsiadm -m node --targetname iqn.2016-01-com.example.tg1 --portal 192.168.100.1
	
	# BEGIN RECORD 2.0-873
	node.name = iqn.2016-01-com.example.tg1
	node.tpgt = 1
	node.startup = manual
	node.leading_login = No
	iface.hwaddress = <empty>
	iface.ipaddress = <empty>
	iface.iscsi_ifacename = default
	iface.net_ifacename = <empty>
	iface.transport_name = tcp
	iface.initiatorname = <empty>
	iface.bootproto = <empty>
	iface.subnet_mask = <empty>
	iface.gateway = <empty>
	iface.ipv6_autocfg = <empty>
	iface.linklocal_autocfg = <empty>
	iface.router_autocfg = <empty>
	iface.ipv6_linklocal = <empty>
	iface.ipv6_router = <empty>
	iface.state = <empty>
	iface.vlan_id = 0
	iface.vlan_priority = 0
	iface.vlan_state = <empty>
	iface.iface_num = 0
	iface.mtu = 0
	iface.port = 0
	node.discovery_address = 192.168.100.1
	node.discovery_port = 3260
	...

Y podemos comprobar que hemos exportado el volumen en /dev/sdb

	fdisk -l
	...
	Disk /dev/sdb: 512 MiB, 536870912 bytes, 1048576 sectors

Podemos ver la conexión realizada en detalle con la siguiente instrucción:

	iscsiadm -m session -P 3
	iSCSI Transport Class version 2.0-870
	version 2.0-873
	Target: iqn.2016-01-com.example.tg1 (non-flash)
		Current Portal: 192.168.100.1:3260,1
		Persistent Portal: 192.168.100.1:3260,1
			**********
			Interface:
			**********
			Iface Name: default
			Iface Transport: tcp
			Iface Initiatorname: iqn.1993-08.org.debian:01:81cc55237ed9
			Iface IPaddress: 192.168.100.2
			Iface HWaddress: <empty>
			Iface Netdev: <empty>
			SID: 1
			iSCSI Connection State: LOGGED IN
			iSCSI Session State: LOGGED_IN
			Internal iscsid Session State: NO CHANGE
			*********
			Timeouts:
			*********
			Recovery Timeout: 120
			Target Reset Timeout: 30
			LUN Reset Timeout: 30
			Abort Timeout: 15
			*****
			CHAP:
			*****
			username: <empty>
			password: ********
			username_in: <empty>
			password_in: ********
			************************
			Negotiated iSCSI params:
			************************
			HeaderDigest: None
			DataDigest: None
			MaxRecvDataSegmentLength: 262144
			MaxXmitDataSegmentLength: 8192
			FirstBurstLength: 65536
			MaxBurstLength: 262144
			ImmediateData: Yes
			InitialR2T: Yes
			MaxOutstandingR2T: 1
			************************
			Attached SCSI devices:
			************************
			Host Number: 3	State: running
			scsi3 Channel 00 Id 0 Lun: 0
			scsi3 Channel 00 Id 0 Lun: 1
				Attached scsi disk sdb		State: running

###Configuración de la autentificación

Para modificar el target y añadir autentificación, tendremos que modificar la configuración del fichero /etc/tgt/targets.conf de la siguiente manera:

	<target iqn.2016-01-com.example.tg1>
		backing-store /dev/vg1/ejem
		incominguser usuario password
	</target>

Reiniciamos el servidor:

	systemctl restart tgt

Ahora en el cliente tenemos que desconectarnos de la sesión actual:

	iscsiadm -m node --targetname iqn.2016-01-com.example.tg1 --portal 192.168.100.1 --logout
	Logging out of session [sid: 1, target: iqn.2016-01-com.example.tg1, portal: 192.168.100.1,3260]
	Logout of [sid: 1, target: iqn.2016-01-com.example.tg1, portal: 192.168.100.1,3260] successful.

Y volver a conectarnos utilizando las credenciales, para ello vamos a modificar la base de datos con el usuario y la contraseña:

	iscsiadm -m node --targetname iqn.2016-01-com.example.tg1 -p 192.168.100.1 -o update -n node.session.auth.username -v usuario
	iscsiadm -m node --targetname iqn.2016-01-com.example.tg1 -p 192.168.100.1 -o update -n node.session.auth.password -v password

Y ya podemos conectar de nuevo:

	iscsiadm -m node --targetname iqn.2016-01-com.example.tg1 --portal 192.168.100.1 --login
	Logging in to [iface: default, target: iqn.2016-01-com.example.tg1, portal: 192.168.100.1,3260] (multiple)
	Login to [iface: default, target: iqn.2016-01-com.example.tg1, portal: 192.168.100.1,3260] successful.

Comprobamos que todo funciona viendo los log del sistema:

	dmesg	

	[ 5268.251073] scsi6 : iSCSI Initiator over TCP/IP
	[ 5269.257242] scsi 6:0:0:0: RAID              IET      Controller       0001 PQ: 0 ANSI: 5
	[ 5269.258750] scsi 6:0:0:0: Attached scsi generic sg1 type 12
	[ 5269.259634] scsi 6:0:0:1: Direct-Access     IET      VIRTUAL-DISK     0001 PQ: 0 ANSI: 5
	[ 5269.261145] sd 6:0:0:1: Attached scsi generic sg2 type 0
	[ 5269.262710] sd 6:0:0:1: [sdb] 1048576 512-byte logical blocks: (536 MB/512 MiB)
	[ 5269.263433] sd 6:0:0:1: [sdb] Write Protect is off
	[ 5269.263435] sd 6:0:0:1: [sdb] Mode Sense: 69 00 00 08
	[ 5269.263720] sd 6:0:0:1: [sdb] Write cache: enabled, read cache: enabled, doesn't support DPO or FUA
	[ 5269.266454]  sdb: sdb1
	[ 5269.269005] sd 6:0:0:1: [sdb] Attached SCSI disk	

