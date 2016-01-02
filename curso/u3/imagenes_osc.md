---
layout: blog
tittle: Curso OpenStack
menu:
  - Unidades
---

## Gestión de imágenes con OSC

### Crear una nueva imagen

	openstack image create --container-format=bare --disk-format=qcow2 \
	 --public --file  trusty-server-cloudimg-amd64-disk1.img ubuntu_14_04
	+------------------+------------------------------------------------------+
	| Field            | Value                                                |
	+------------------+------------------------------------------------------+
	| checksum         | 272ae9d6c48c937cbdf327bd44e4313c                     |
	| container_format | bare                                                 |
	| created_at       | 2016-01-01T10:14:10Z                                 |
	| disk_format      | qcow2                                                |
	| file             | /v2/images/1fa049be-667f-4de1-bf7d-50066424a0b0/file |
	| id               | 1fa049be-667f-4de1-bf7d-50066424a0b0                 |
	| min_disk         | 0                                                    |
	| min_ram          | 0                                                    |
	| name             | ubuntu_14_04                                         |
	| owner            | fb72e212f8804a5cbebd74e84dff89fa                     |
	| protected        | False                                                |
	| schema           | /v2/schemas/image                                    |
	| size             | 258671104                                            |
	| status           | active                                               |
	| updated_at       | 2016-01-01T10:14:13Z                                 |
	| virtual_size     | None                                                 |
	| visibility       | public                                               |
	+------------------+------------------------------------------------------+

### Listar las imágenes disponibles

	openstack image list
	...
	| 1fa049be-667f-4de1-bf7d-50066424a0b0 | ubuntu_14_04                    |
	...

### Ver detalles de la imagen

	openstack image show 1fa049be-667f-4de1-bf7d-50066424a0b0
	+------------------+------------------------------------------------------+
	| Field            | Value                                                |
	+------------------+------------------------------------------------------+
	| checksum         | 272ae9d6c48c937cbdf327bd44e4313c                     |
	| container_format | bare                                                 |
	| created_at       | 2016-01-01T10:14:10Z                                 |
	| disk_format      | qcow2                                                |
	| file             | /v2/images/1fa049be-667f-4de1-bf7d-50066424a0b0/file |
	| id               | 1fa049be-667f-4de1-bf7d-50066424a0b0                 |
	| min_disk         | 0                                                    |
	| min_ram          | 0                                                    |
	| name             | ubuntu_14_04                                         |
	| owner            | fb72e212f8804a5cbebd74e84dff89fa                     |
	| protected        | False                                                |
	| schema           | /v2/schemas/image                                    |
	| size             | 258671104                                            |
	| status           | active                                               |
	| updated_at       | 2016-01-01T10:14:13Z                                 |
	| virtual_size     | None                                                 |
	| visibility       | public                                               |
	+------------------+------------------------------------------------------+


### Borrar la imágen

	openstack image delete 1fa049be-667f-4de1-bf7d-50066424a0b0

###Documentación

* [OpenStackClient image](http://docs.openstack.org/developer/python-openstackclient/command-objects/image.html)