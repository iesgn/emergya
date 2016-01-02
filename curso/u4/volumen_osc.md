---
layout: blog
tittle: Curso OpenStack
menu:
  - Unidades
---

## Gestión de volúmenes con OSC

### Asociación de un volumen a una instancia


1. Vamos a crear un volumen de 1 GiB:

		$ openstack volume create --size 1 disco1
		+---------------------+--------------------------------------+
		| Field               | Value                                |
		+---------------------+--------------------------------------+
		| attachments         | []                                   |
		| availability_zone   | nova                                 |
		| bootable            | false                                |
		| created_at          | 2016-01-02T12:27:19.002064           |
		| display_description | None                                 |
		| display_name        | disco1                               |
		| encrypted           | False                                |
		| id                  | fb7d4d30-2690-4d5b-91a6-16418db21e58 |
		| multiattach         | false                                |
		| properties          |                                      |
		| size                | 1                                    |
		| snapshot_id         | None                                 |
		| source_volid        | None                                 |
		| status              | creating                             |
		| type                | lvmdriver-1                          |
		+---------------------+--------------------------------------+

2. A continuación vamos a asociarlo a nuestra instancia:

		$ openstack server add volume primera disco1

3. Comprobamos que el volumen aparece asociado a la instancia:

		$ openstack volume list
		+--------------------------------------+--------------+--------+------+----------------------------------+
		| ID                                   | Display Name | Status | Size | Attached to                      |
		+--------------------------------------+--------------+--------+------+----------------------------------+
		| fb7d4d30-2690-4d5b-91a6-16418db21e58 | disco1       | in-use |    1 | Attached to primera on /dev/vdb  |
		+--------------------------------------+--------------+--------+------+----------------------------------+


###Documentación

* [OpenStackClient image](http://docs.openstack.org/developer/python-openstackclient/command-objects/image.html)