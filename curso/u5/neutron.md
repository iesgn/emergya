---
layout: blog
tittle: Curso OpenStack
menu:
  - Unidades
---

###Gestión de redes con horizon

Partimos del siguiente esquema de red con el que hemos estado trabajando:

![red](img/neutron/01.png)

En esta demostración vamos a crear una nueva red, una nueva subred y un nuevo router. El router estará conectado a la red externa y a la nueva red creada. El esquema de red sería el siguiente:

![red](img/neutron/02.png)

1. En el apartado **Redes** vamos a crear una nueva red.

	![red](img/neutron/03.png)

	Los datos que vamos a introducir son los siguientes:

	* **Nombre de la red**
	* **Estado de administración**: Podemos indicar si la red va a esta habilitada (up/arriba) o deshabilitada (down/abajo).
	* **Crear subred**: Indicamos si vamos a crear una subred asociada.

	A continuación indicamos los datos de la subred asociada:

	![red](img/neutron/04.png)

	* **Nombre de subred**: El usuario deberá indicar un nombre a proporcionarle a la subred.
	* **Direcciones de red**: Se debe introducir un CIDR válido, el cual indicará un bloque de direcciones IPs.
	* **Versión IP**: Se especifica la versión de las direcciones IPs.
	* **IP de la puerta de enlace**: Dirección IP que especificamos para la puerta de enlace. Debe pertenecer a las direcciones del CIDR. Si se especifica este valor tenemos que comprobar que esta dirección no se superpone a alguna dirección del pool de direcciones. Si no se indica, se cogera la primera dirección del pool de direcciones.
	* **Deshabilitar la puerta de enlace**: Si escogemos esta opción no se enviará gateway a las instancias que se conecten a esta red.

	![red](img/neutron/05.png)	

	* **Habilitar DHCP**: Con esta opción activamos o desactivamos el servidor DHCP asociado a esta red.
	* **Pool de direcciones**: Indica el rango de direcciones IPs que se utilizarán para asignar IPs a los servidores virtuales. Este rango debe pertenecer a las direcciones del CIDR. 	
	* **Servidores DNS**: El usuario puede especificar una lista de dns. Este campo se introduce separado por comas.
	* Rutas de hosts**: Son las rutas de encaminamiento que el servidor DHCP va a enviar a las instancias que se conecten a la red.

2. Creamos un router: Para ello elegimos: **Red**->**Routers**->**Crear router**, escogemos la red externa a la que va a estra conectado nuestro router:

	![red](img/neutron/06.png)

	A continuación vamos a conectar la nueva red al router que hemos creado, para ello seleccionamos el router y las opciones **Interfaces**->**Añadir Interfaz**:

	![red](img/neutron/07.png)

	En el apartado **Dirección IP (opcional)** podemos indicar la dirección IP que toma esta nueva interfaz, son se especifica se cogerá la primera del pool de direcciones que hemos indicado al configurar la subred.

3. Una vez creado nuestra nueva red, conectada a un nuevo router vamos a crear una instancia en la nueva red (suponemos que en nuestra primera red tenemos creada una instancia) y vamos a comprobar que se le puede asociar una ip flotante, además vamos a comprobar que no podemos hacer ping a otra instancia que está conectada a la otra red.

    A la hora de crear la nueva instancia tenemos que asegurarnos que la conectamos a la nueva red.

    ![red](img/neutron/08.png)

    Podemos comprobar en la siguiente imagen que la nueva instancia tiene una IP fija en el rango de la nueva red (20.0.0.0/24) y que tiene asociada una ip pública.

    ![red](img/neutron/09.png)

    Por último podemos comprobar que las instancias no tienen conectividad. Si accedemos a una de ella, y hacemos ping a la ip fija de la otra, evidentemente, no tendremos conectividad. Evidentemente si podemos acceder de las ip flotantes:		

		$ ssh -i clave_demo.pem cirros@192.168.0.5
		$ ping 10.0.0.3
		PING 10.0.0.3 (10.0.0.3): 56 data bytes
		^C
		--- 10.0.0.3 ping statistics ---
		5 packets transmitted, 0 packets received, 100% packet loss
		$ ping 192.168.0.3
		PING 192.168.0.3 (192.168.0.3): 56 data bytes
		64 bytes from 192.168.0.3: seq=0 ttl=62 time=2.946 ms
		64 bytes from 192.168.0.3: seq=1 ttl=62 time=0.859 ms
		^C
		--- 192.168.0.3 ping statistics ---
		2 packets transmitted, 2 packets received, 0% packet loss
		round-trip min/avg/max = 0.859/1.902/2.946 ms







