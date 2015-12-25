---
layout: blog
tittle: Curso OpenStack (Emergya)
menu:
  - Unidades
---

##Crear una instancia a partir de una imagen

El objetivo de esta sección es mostrar es la creación de una instancia GNU/Linux
a partir de una de las imágenes disponibles. Para ello accedemos al apartado
**Instancias** > **Lanzar instancia** y a continuación tenemos
que indicar los siguientes datos:

* El primer panel es utilizado para indicar el tipo de instancia y la imagen que
se aplicarán al servidor. Los valores a introducir son:
  * Nombre : El nombre que pondremos al servidor virtual o instancia.
  * Sabor: Se debe elegir el nombre del tipo de instancia o sabor a
  aplicar en la creación, las características del sabor elegido se muestran en 
  el panel lateral.
  * Recuento de instancias: Número de instancias que vamos a crear.
  * Origen de arranque de la instancia: Indica si vamos a crear el servidor a partir de una
  imagen, una instantánea o un volumen. En
  este caso será a partir de una imagen, veremos el resto de casos más
  adelante.
  * Nombre de la imagen: Indicamos la imagen virtual, la instantánea o el volumen concreto
  que queremos utilizar para instalar el sistema operativo de nuestra instancia.
  
![instancia](img/instancias1/01.png)


* En el segundo panel se gestionan temas de seguridad. Los datos a indicar son:
  * Par de claves: Se selecciona una clave pública de una lista de claves
  públicas creadas previamente en el apartado **Acceso y Seguridad**. 
  * Se selecciona uno o más grupos de seguridad para aplicar en la instancia a la
  hora de gestionar el tráfico en la red.

![instancia](img/instancias1/02.png)



* En el segundo panel se indican las redes a las cuales queremos que pertenezca
nuestro servidor, es decir donde nuestro servidor virtual tendrá una IP fija.


![instancia](img/demo2_4.png)



* De forma general no es necesario indicar nada en los dos paneles adicionales
"Información de usuario" y "Metadata" por lo que ya es posible pulsar el botón
"Terminar" y al cabo de unos segundos podemos ver que nuestra instancia ha
sido creada, que tiene asignada una IP fija:


![instancia](img/demo2_6.png)


* Por último, para poder acceder a la instancia desde el exterior tenemos que
asignar a la instancia una IP flotante, una IP Pública, para ello:
  * Elegimos la opción **IP públicas** en el apartado **Administrador de
  seguridad** y asignamos una nueva IP. 
  * Desde esa misma pantalla o desde la **Adminstración de servidores**,
  asignamos esa nueva IP a la instancia que acabamos de crear.


![ip](img/demo2_7.png)	


* Recuerda que una vez que la IP pública no esté asignada a una instancia se
puede liberar.
* El último paso es simplemente acceder a la instancia utilizando la clave 
privada correspondiente a la clave pública que se ha inyectado en la
instancia:


![ip](img/demo2_8.png)	
