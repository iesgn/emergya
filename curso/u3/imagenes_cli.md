---
layout: blog
tittle: Curso OpenStack
menu:
  - Unidades
---

## Gestión de imágenes con glance CLI

### Crear una nueva imagen

		glance image-create --name='cirros image'  \
		  --container-format=bare --disk-format=qcow2 < cirros-0.3.3-x86_64-disk.img

### Listar las imágenes disponibles

		glance image-list
		...
		| 0dc74a8f-1dca-438e-855e-d694b9429176 | cirros image                                           | qcow2       | bare             | 13200896    | active |
		...

### Ver detalles de la imagen

		 glance image-show 'cirros image'
		+------------------+--------------------------------------+
		| Property         | Value                                |
		+------------------+--------------------------------------+
		| checksum         | 133eae9fb1c98f45894a4e60d8736619     |
		| container_format | bare                                 |
		| created_at       | 2015-06-04T17:11:43                  |
		| deleted          | False                                |
		| disk_format      | qcow2                                |
		| id               | 0dc74a8f-1dca-438e-855e-d694b9429176 |
		| is_public        | True                                 |
		| min_disk         | 0                                    |
		| min_ram          | 0                                    |
		| name             | cirros image                         |
		| owner            | 1a0b324cc09c40c79286fc1bc63c5833     |
		| protected        | False                                |
		| size             | 13200896                             |
		| status           | active                               |
		| updated_at       | 2015-06-04T17:19:29                  |
		+------------------+--------------------------------------+

### Compartir imágenes entre proyectos

		glance member-create IMAGEN_ID PROYECTO_ID

### Borrar la imágen

		glance image-delete 'cirros image'

### Resumen de comandos

		glance help

		image-create        Create a new image.
		image-delete        Delete specified image(s).
		image-download      Download a specific image.
		image-list          List images you can access.
		image-show          Describe a specific image.
		image-update        Update a specific image.
		member-create       Share a specific image with a tenant.
		member-delete       Remove a shared image from a tenant.
		member-list         Describe sharing permissions by image or tenant.
		help                Display help about this program or one of its subcommands.


## Gestión de imágenes con OSC

### Crear una nueva imagen

	openstack image create --container-format=bare --disk-format=qcow2 --public --file  trusty-server-cloudimg-amd64-disk1.img ubuntu_14_04
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