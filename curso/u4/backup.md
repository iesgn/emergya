---
layout: blog
tittle: Curso de OpenStack
menu:
  - Unidades
---

##Copias de seguridad de volumes

El cliente cinder proporciona las herramientas necesarias para crear una copia de seguridad de un volumen. Las copias de seguridad se guardar como objetos en el contenedor de objetos swift. Por defecto se utiliza swift como almacen de copias de seguridad, aunque se puede configurar otros backend para realizar las copias de seguridad, por ejemplo una coarpeta compartida por NFS.

###Crear una copia de seguridad

Para crear un copia se seguridad de un volumen ejecutamos la siguiente instrucción:

Partimos del siguiente volumen, que hemos formateado y creado un fichero desde una instancia:

	$ cinder list
	+--------------------------------------+-----------+--------+------+-------------+----------+-------------+
	|                  ID                  |   Status  |  Name  | Size | Volume Type | Bootable | Attached to |
	+--------------------------------------+-----------+--------+------+-------------+----------+-------------+
	| 917ef4cc-784d-4803-a19a-984b847b9f1e | available | disco1 |  1   | lvmdriver-1 |  false   |             |
	+--------------------------------------+-----------+--------+------+-------------+----------+-------------+

Vamos a hacer una copia de seguridad:

	$ cinder backup-create 917ef4cc-784d-4803-a19a-984b847b9f1e
	+-----------+--------------------------------------+
	|  Property |                Value                 |
	+-----------+--------------------------------------+
	|     id    | 77e2430d-afda-4733-bf55-6d150555b75f |
	|    name   |                 None                 |
	| volume_id | 917ef4cc-784d-4803-a19a-984b847b9f1e |
	+-----------+--------------------------------------+


Vemos la lista de copias de seguridad con:

	$ cinder backup-list
	+--------------------------------------+--------------------------------------+-----------+------+------+--------------+---------------+
	|                  ID                  |              Volume ID               |   Status  | Name | Size | Object Count |   Container   |
	+--------------------------------------+--------------------------------------+-----------+------+------+--------------+---------------+
	| 77e2430d-afda-4733-bf55-6d150555b75f | 917ef4cc-784d-4803-a19a-984b847b9f1e | available | None |  1   |      22      | volumebackups |
	+--------------------------------------+--------------------------------------+-----------+------+------+--------------+---------------+

Y finalmente podemos pedir información sobre la copia de seguridad:

	$ cinder backup-show 77e2430d-afda-4733-bf55-6d150555b75f
	+-------------------+--------------------------------------+
	|      Property     |                Value                 |
	+-------------------+--------------------------------------+
	| availability_zone |                 nova                 |
	|     container     |            volumebackups             |
	|     created_at    |      2016-01-08T16:39:47.000000      |
	|    description    |                 None                 |
	|    fail_reason    |                 None                 |
	|         id        | 77e2430d-afda-4733-bf55-6d150555b75f |
	|        name       |                 None                 |
	|    object_count   |                  22                  |
	|        size       |                  1                   |
	|       status      |              available               |
	|     volume_id     | 917ef4cc-784d-4803-a19a-984b847b9f1e |
	+-------------------+--------------------------------------+

Para comprobar que la copia de seguridad se ha guardado en swifit ejecutamos la siguientes intrucciones:

	$ swift list
	volumebackups

	$ swift list volumebackups
	volume_917ef4cc-784d-4803-a19a-984b847b9f1e/20160108163947/az_nova_backup_77e2430d-afda-4733-bf55-6d150555b75f-00001
	volume_917ef4cc-784d-4803-a19a-984b847b9f1e/20160108163947/az_nova_backup_77e2430d-afda-4733-bf55-6d150555b75f-00002
	volume_917ef4cc-784d-4803-a19a-984b847b9f1e/20160108163947/az_nova_backup_77e2430d-afda-4733-bf55-6d150555b75f-00003
	volume_917ef4cc-784d-4803-a19a-984b847b9f1e/20160108163947/az_nova_backup_77e2430d-afda-4733-bf55-6d150555b75f-00004
	volume_917ef4cc-784d-4803-a19a-984b847b9f1e/20160108163947/az_nova_backup_77e2430d-afda-4733-bf55-6d150555b75f-00005
	volume_917ef4cc-784d-4803-a19a-984b847b9f1e/20160108163947/az_nova_backup_77e2430d-afda-4733-bf55-6d150555b75f-00006
	volume_917ef4cc-784d-4803-a19a-984b847b9f1e/20160108163947/az_nova_backup_77e2430d-afda-4733-bf55-6d150555b75f-00007
	volume_917ef4cc-784d-4803-a19a-984b847b9f1e/20160108163947/az_nova_backup_77e2430d-afda-4733-bf55-6d150555b75f-00008
	volume_917ef4cc-784d-4803-a19a-984b847b9f1e/20160108163947/az_nova_backup_77e2430d-afda-4733-bf55-6d150555b75f-00009
	volume_917ef4cc-784d-4803-a19a-984b847b9f1e/20160108163947/az_nova_backup_77e2430d-afda-4733-bf55-6d150555b75f-00010
	volume_917ef4cc-784d-4803-a19a-984b847b9f1e/20160108163947/az_nova_backup_77e2430d-afda-4733-bf55-6d150555b75f-00011
	volume_917ef4cc-784d-4803-a19a-984b847b9f1e/20160108163947/az_nova_backup_77e2430d-afda-4733-bf55-6d150555b75f-00012
	volume_917ef4cc-784d-4803-a19a-984b847b9f1e/20160108163947/az_nova_backup_77e2430d-afda-4733-bf55-6d150555b75f-00013
	volume_917ef4cc-784d-4803-a19a-984b847b9f1e/20160108163947/az_nova_backup_77e2430d-afda-4733-bf55-6d150555b75f-00014
	volume_917ef4cc-784d-4803-a19a-984b847b9f1e/20160108163947/az_nova_backup_77e2430d-afda-4733-bf55-6d150555b75f-00015
	volume_917ef4cc-784d-4803-a19a-984b847b9f1e/20160108163947/az_nova_backup_77e2430d-afda-4733-bf55-6d150555b75f-00016
	volume_917ef4cc-784d-4803-a19a-984b847b9f1e/20160108163947/az_nova_backup_77e2430d-afda-4733-bf55-6d150555b75f-00017
	volume_917ef4cc-784d-4803-a19a-984b847b9f1e/20160108163947/az_nova_backup_77e2430d-afda-4733-bf55-6d150555b75f-00018
	volume_917ef4cc-784d-4803-a19a-984b847b9f1e/20160108163947/az_nova_backup_77e2430d-afda-4733-bf55-6d150555b75f-00019
	volume_917ef4cc-784d-4803-a19a-984b847b9f1e/20160108163947/az_nova_backup_77e2430d-afda-4733-bf55-6d150555b75f-00020
	volume_917ef4cc-784d-4803-a19a-984b847b9f1e/20160108163947/az_nova_backup_77e2430d-afda-4733-bf55-6d150555b75f-00021
	volume_917ef4cc-784d-4803-a19a-984b847b9f1e/20160108163947/az_nova_backup_77e2430d-afda-4733-bf55-6d150555b75f_metadata
	volume_917ef4cc-784d-4803-a19a-984b847b9f1e/20160108163947/az_nova_backup_77e2430d-afda-4733-bf55-6d150555b75f_sha256file

###Resturar una copia de seguridad

Para restaurar una nueva copia de seguridad a un nuevo volumen, ejecutamos la siguiente instrucción:

	$ cinder backup-restore  77e2430d-afda-4733-bf55-6d150555b75f

Podemos ver el proceso de restauración con la siguiente instrucción:

	$ cinder list
	+--------------------------------------+------------------+-----------------------------------------------------+------+-------------+----------+-------------+
	|                  ID                  |      Status      |                         Name                        | Size | Volume Type | Bootable | Attached to |
	+--------------------------------------+------------------+-----------------------------------------------------+------+-------------+----------+-------------+
	| 917ef4cc-784d-4803-a19a-984b847b9f1e |    available     |                        disco1                       |  1   | lvmdriver-1 |  false   |             |
	| ebff83f2-cec8-429d-af8a-67e9d012ef5e | restoring-backup | restore_backup_77e2430d-afda-4733-bf55-6d150555b75f |  1   | lvmdriver-1 |  false   |             |
	+--------------------------------------+------------------+-----------------------------------------------------+------+-------------+----------+-------------+	

Y finalmente vemos que se ha creado un  nuevo volumen restaurado desde la copia de seguridad:

	$ cinder list
	+--------------------------------------+-----------+-----------------------------------------------------+------+-------------+----------+-------------+
	|                  ID                  |   Status  |                         Name                        | Size | Volume Type | Bootable | Attached to |
	+--------------------------------------+-----------+-----------------------------------------------------+------+-------------+----------+-------------+
	| 917ef4cc-784d-4803-a19a-984b847b9f1e | available |                        disco1                       |  1   | lvmdriver-1 |  false   |             |
	| ebff83f2-cec8-429d-af8a-67e9d012ef5e | available | restore_backup_77e2430d-afda-4733-bf55-6d150555b75f |  1   | lvmdriver-1 |  false   |             |
	+--------------------------------------+-----------+-----------------------------------------------------+------+-------------+----------+-------------+
