---
layout: blog
tittle: Curso OpenStack
menu:
  - Unidades
---

## Gestión de volúmenes con OpenStack client (OSC)

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

4. Para desasociar el volumen de la intancia:

		$ openstack server remove volume primera disco1

### Creación de una instancia con el disco raíz sobre un volumen

1. Visualizamos la lista de imágenes y de sabores que tenemos en 
nuestro sistema: 

    	openstack image list
    	openstack flavor list

2. Creamos un volumen *arrancable* de 8 GiB que contenga la imagen:

        $ openstack volume create --size 3 --image 30e0af35-6cd4-4d9e-a372-606bf3deea0a disco_cirros
		+---------------------+--------------------------------------+
		| Field               | Value                                |
		+---------------------+--------------------------------------+
		| attachments         | []                                   |
		| availability_zone   | nova                                 |
		| bootable            | false                                |
		| created_at          | 2016-01-02T12:35:51.491128           |
		| display_description | None                                 |
		| display_name        | disco_cirros                         |
		| encrypted           | False                                |
		| id                  | 4ebec988-058f-4df9-90fc-61144669de33 |
		| image_id            | 30e0af35-6cd4-4d9e-a372-606bf3deea0a |
		| multiattach         | false                                |
		| properties          |                                      |
		| size                | 3                                    |
		| snapshot_id         | None                                 |
		| source_volid        | None                                 |
		| status              | creating                             |
		| type                | lvmdriver-1                          |
		+---------------------+--------------------------------------+


    Aunque inicialmente al crearse el volumen nos muestra el parámetro *booteable* con el valor *false*, una vez se ha terminado de crear podemos comprobar que OpenStack reconoce el volumen como arrancable al contener un sistema operativo:

        $ openstack volume show disco_cirros
		+---------------------------------------+--------------------+
		| Field                                 | Value              |
		+---------------------------------------+--------------------+
		| attachments           | []                                 |
		| availability_zone     | nova                               |
		| bootable              | true                               |
		...                                                           
		+---------------------------------------+--------------------+                                                                                                                                                                                                                                   |
3. Creamos una nueva instancia con este volumen

        $ openstack server create --flavor m1.tiny \
        --volume disco_cirros \
        --security-group default \
        --key-name clave_demo \
        --nic net-id=ef06b714-8b3a-4af4-bca0-d0eae224c0b9 \
        instancia_cirros
		+--------------------------------------+----------------------------------------------------+
		| Field                                | Value                                              |
		+--------------------------------------+----------------------------------------------------+
		| OS-DCF:diskConfig                    | MANUAL                                             |
		| OS-EXT-AZ:availability_zone          | nova                                               |
		| OS-EXT-STS:power_state               | 0                                                  |
		| OS-EXT-STS:task_state                | scheduling                                         |
		| OS-EXT-STS:vm_state                  | building                                           |
		| OS-SRV-USG:launched_at               | None                                               |
		| OS-SRV-USG:terminated_at             | None                                               |
		| accessIPv4                           |                                                    |
		| accessIPv6                           |                                                    |
		| addresses                            |                                                    |
		| adminPass                            | thFaau5y6HyA                                       |
		| config_drive                         |                                                    |
		| created                              | 2016-01-02T12:43:25Z                               |
		| flavor                               | m1.tiny (1)                                        |
		| hostId                               |                                                    |
		| id                                   | bcc209ca-8f15-42c6-b707-bc13870ea80d               |
		| image                                |                                                    |
		| key_name                             | clave_demo                                         |
		| name                                 | instancia_cirros                                   |
		| os-extended-volumes:volumes_attached | [{u'id': u'4ebec988-058f-4df9-90fc-61144669de33'}] |
		| progress                             | 0                                                  |
		| project_id                           | e2661319dea4407cbc9370ecf995d1ca                   |
		| properties                           |                                                    |
		| security_groups                      | [{u'name': u'default'}]                            |
		| status                               | BUILD                                              |
		| updated                              | 2016-01-02T12:43:25Z                               |
		| user_id                              | f219b0ebb4d34a249ec7b9310f55e980                   |
		+--------------------------------------+----------------------------------------------------+

### Creación de una instantánea de un volumen

1. Creamos una instantánea de volumen

		$ openstack snapshot create --name copia_disco1 disco1
		+---------------------+--------------------------------------+
		| Field               | Value                                |
		+---------------------+--------------------------------------+
		| created_at          | 2016-01-02T12:52:36.061650           |
		| display_description | None                                 |
		| display_name        | copia_disco1                         |
		| id                  | 5e471b37-7fb3-4830-bae3-7d3eead006d1 |
		| properties          |                                      |
		| size                | 1                                    |
		| status              | creating                             |
		| volume_id           | fb7d4d30-2690-4d5b-91a6-16418db21e58 |
		+---------------------+--------------------------------------+

2. Listamos las instantáneas

		$ openstack snapshot list
		+--------------------------------------+--------------+-------------+-----------+------+
		| ID                                   | Name         | Description | Status    | Size |
		+--------------------------------------+--------------+-------------+-----------+------+
		| 5e471b37-7fb3-4830-bae3-7d3eead006d1 | copia_disco1 | None        | available |    1 |
		+--------------------------------------+--------------+-------------+-----------+------+

3. Creamos un nuevo volumen a partir de la instantánea

		$ openstack volume create --size 1 --snapshot 5e471b37-7fb3-4830-bae3-7d3eead006d1 disco2
		+---------------------+--------------------------------------+
		| Field               | Value                                |
		+---------------------+--------------------------------------+
		| attachments         | []                                   |
		| availability_zone   | nova                                 |
		| bootable            | false                                |
		| created_at          | 2016-01-02T12:56:07.495553           |
		| display_description | None                                 |
		| display_name        | disco2                               |
		| encrypted           | False                                |
		| id                  | d61c9f86-efc6-4185-950a-c4ac4610594c |
		| multiattach         | false                                |
		| properties          |                                      |
		| size                | 1                                    |
		| snapshot_id         | 5e471b37-7fb3-4830-bae3-7d3eead006d1 |
		| source_volid        | None                                 |
		| status              | creating                             |
		| type                | lvmdriver-1                          |
		+---------------------+--------------------------------------+


4. Borramos el snapshot y el volumen creado

	Si intentamos borrar el volumen desde el que hemos creado la instantánea:

		$ openstack volume delete disco1
		Invalid volume: Volume still has 1 dependent snapshots. (HTTP 400) (Request-ID: req-917a4f06-8874-4e59-a693-122006454d90)

	Debemos borrar primero el snapshot y posteriormente el volumen:

		$ openstack snapshot delete copia_disco1
		$ openstack volume delete disco1

###Extender el tamaño de un volumen

Vamos a redimensionar el tamaño de un volumen:

		$ openstack volume set --size 2 disco2
		$ openstack volume list
		+--------------------------------------+--------------+-----------+------+-------------+
		| ID                                   | Display Name | Status    | Size | Attached to |
		+--------------------------------------+--------------+-----------+------+-------------+
		| d61c9f86-efc6-4185-950a-c4ac4610594c | disco2       | available |    2 |             |
		+--------------------------------------+--------------+-----------+------+-------------+

###Documentación

* [OpenStackClient volume](http://docs.openstack.org/developer/python-openstackclient/command-objects/volume.html)
* [OpenStackClient snapshot](http://docs.openstack.org/developer/python-openstackclient/command-objects/snapshot.html)