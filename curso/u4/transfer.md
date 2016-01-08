---
layout: blog
tittle: Curso OpenStack
menu:
  - Unidades
---

##Transferencias de volúmenes

Esta operación nos permite transferir un volumen desde un proyecto a otro. 

###Crear una transferencia

Vamos a trabajar con el usuario demo, que tiene creado un volumen.

	$ source demo-openrc.sh	

	$ cinder list
	+--------------------------------------+-------------------+--------+------+-------------+----------+-------------+
	|                  ID                  |       Status      |  Name  | Size | Volume Type | Bootable | Attached to |
	+--------------------------------------+-------------------+--------+------+-------------+----------+-------------+
	| 917ef4cc-784d-4803-a19a-984b847b9f1e | awaiting-transfer | disco1 |  1   | lvmdriver-1 |  false   |             |
	+--------------------------------------+-------------------+--------+------+-------------+----------+-------------+

Vamos a crear una transferencia, con las siguientes instrucciones:

	$ cinder transfer-create 917ef4cc-784d-4803-a19a-984b847b9f1e
	+------------+--------------------------------------+
	|  Property  |                Value                 |
	+------------+--------------------------------------+
	|  auth_key  |           408af7d4a1441587           |
	| created_at |      2016-01-08T17:07:03.412265      |
	|     id     | d24d5659-40e9-446c-9437-238fc8868571 |
	|    name    |                 None                 |
	| volume_id  | 917ef4cc-784d-4803-a19a-984b847b9f1e |
	+------------+--------------------------------------+
	
	$ cinder transfer-list
	+--------------------------------------+--------------------------------------+------+
	|                  ID                  |              Volume ID               | Name |
	+--------------------------------------+--------------------------------------+------+
	| d24d5659-40e9-446c-9437-238fc8868571 | 917ef4cc-784d-4803-a19a-984b847b9f1e | None |
	+--------------------------------------+--------------------------------------+------+

###Realizar una transferencia

A continuación vamos a trabajar con otro usuario y otro proyecto, a donde vamos a realizar la transferencia del volumen.

	$ source demo2-openrc.sh

Comprobamos que en este proyecto no hay ningún volumen:

	$ cinder list
	+----+--------+------+------+-------------+----------+-------------+
	| ID | Status | Name | Size | Volume Type | Bootable | Attached to |
	+----+--------+------+------+-------------+----------+-------------+
	+----+--------+------+------+-------------+----------+-------------+

