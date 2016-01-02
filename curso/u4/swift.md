---
layout: blog
tittle: Curso OpenStack
menu:
  - Unidades
---

### Gestión de contenedores y objetos con horizon

1. Crear un contenedor

	Un contenedor es equivalente a un directorio en un sistema de ficheros, sirve para organizar objetos, pero tiene una diferencia fundamental con el directorio: No se pueden anidar contenedores.

	Para crear un nuevo contenedor accedemos a la opción **Almacén de objetos**->**Contenedores**->**Crear contenedor**:

	![swift](img/swifit/01.png)

	Indicamos el nombre del contnedor y el ACL (Utilizada para gestionar los permisos que otros usuarios pueden tener sobre cada contenedor, no sobre cada objeto) de lectura: Pública o Privada. 

2. Podemos crear una pseudo-carpeta para agrupar los objetos

	![swift](img/swifit/02.png)	

3. Subimos un objeto

	Un objeto es un elemento que se almacena, suele estar relacionado con un fichero o archivo, pero además incluye una serie de metadatos.

	![swift](img/swifit/03.png)		

	Y podemos ver el objeto que tenemos en nuestro contenedor:

	![swift](img/swifit/04.png)	

4. Accedemos al objeto guradado

	Podemos acceder desde un navegador a dicha imagen, utilizando el endpoint de swift, e indicando contenedor y objeto, en este caso sería:

		http://192.168.27.100:8080/v1/AUTH_e2661319dea4407cbc9370ecf995d1ca/contenedor/logo.png

	![swift](img/swifit/05.png)