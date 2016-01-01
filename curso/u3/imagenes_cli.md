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

		