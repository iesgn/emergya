---
layout: blog
tittle: Curso OpenStack
menu:
  - Unidades
---

###Gestión de objetos con OpenStack client (OSC)

1. Creando y trabajando con un contenedor

		$ openstack container create contenedor
		+---------------------------------------+------------+------------------------------------+
		| account                               | container  | x-trans-id                         |
		+---------------------------------------+------------+------------------------------------+
		| AUTH_e2661319dea4407cbc9370ecf995d1ca | contenedor | txf373ecdd6c60452c84b7a-005687dcbc |
		+---------------------------------------+------------+------------------------------------+

	Para listar todos los contenedores:

		$ openstack container list
		+------------+
		| Name       |
		+------------+
		| contenedor |
		+------------+

	Subimos un objeto al contenedor:

		$ openstack object create contenedor logo.png 
		+----------+------------+----------------------------------+
		| object   | container  | etag                             |
		+----------+------------+----------------------------------+
		| logo.png | contenedor | 5104ff477768b048059c1ae2b4aa89ac |
		+----------+------------+----------------------------------+

	Para listar los objetos de un contenedor:

		$ openstack object list contenedor
		+----------+
		| Name     |
		+----------+
		| logo.png |
		+----------+

	Puedes obtener información de un contenedor o un objeto:

		$ openstack container show contenedor
		+--------------+------------+
		| Field        | Value      |
		+--------------+------------+
		| account      | None       |
		| bytes_used   | 121427     |
		| container    | contenedor |
		| object_count | 1          |
		| read_acl     | None       |
		| sync_key     | None       |
		| sync_to      | None       |
		| write_acl    | None       |
		+--------------+------------+


		$ openstack object show contenedor logo.png
		+----------------+------------------------------------+
		| Field          | Value                              |
		+----------------+------------------------------------+
		| accept-ranges  | bytes                              |
		| account        | None                               |
		| connection     | keep-alive                         |
		| container      | contenedor                         |
		| content-length | 121427                             |
		| content-type   | image/png                          |
		| date           | Sat, 02 Jan 2016 14:26:04 GMT      |
		| etag           | 5104ff477768b048059c1ae2b4aa89ac   |
		| last-modified  | Sat, 02 Jan 2016 14:24:17 GMT      |
		| object         | logo.png                           |
		| x-timestamp    | 1451744656.42856                   |
		| x-trans-id     | tx417e992993e148508c1d7-005687ddfc |
		+----------------+------------------------------------+

2. Descargar un objeto

		$ openstack object save contenedor logo.png

3. Acceder a un objeto

	
	Podemos acceder desde un navegador a dicha imagen, utilizando el endpoint de swift, e indicando contenedor y objeto, en este caso sería:

		http://192.168.27.100:8080/v1/AUTH_e2661319dea4407cbc9370ecf995d1ca/contenedor/logo.png

	Para que se accesible la imagen tenemos que dar permiso de lectura al contenedor. La versión del cliente openstack que estamos usando para este ejemplo todavía no tiene implementado esta funcionalidad, pero si buscamos en la documentación encontraremos la siguiente instrucción para modificar la ACL de lectura:
	

		$ openstack container set read_acl='.r:*' contenedor


4. Borrar un conetenedor

		$ openstack object delete contenedor logo.png
		$ openstack container delete contenedor

###Documentación

* [OpenStackClient container](http://docs.openstack.org/developer/python-openstackclient/command-objects/container.html)
* [OpenStackClient object](http://docs.openstack.org/developer/python-openstackclient/command-objects/object.html)