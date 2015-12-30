---
layout: blog
tittle: Curso OpenStack
menu:
  - Unidades
---

## Gestión de instancias con nova CLI

El objetivo de esta demostración es la creación de una instancia
usando el cliente nova. Partimos de un escenario donde ya tenemos
instalado el cliente (paquete *python-novaclient*) y tenemos cargado
el fichero de credenciales (demo-openrc.sh). 

Vamos a ver todos los pasos que necesitamos para crear una instancia: 

1. Solicitamos a nova que cree un nuevo par de claves ssh (que
redirigimos al fichero clave-acceso.pem) y modificamos los permisos de
la clave privada de forma adecuada:

        nova keypair-add clave_acceso > ~/.ssh/clave-acceso.pem
        chmod 400 ~/.ssh/clave-acceso.pem

2. Creamos un nuevo grupo seguridad (cortafuegos) y creamos una regla
que nos permita el acceso por ssh. 

        nova secgroup-create gr_seguridad "Reglas de seguridad"
        +--------------------------------------+--------------+---------------------+
        | Id                                   | Name         | Description         |
        +--------------------------------------+--------------+---------------------+
        | 0840438d-573c-4392-af20-1c936f260abc | gr_seguridad | Reglas de seguridad |
        +--------------------------------------+--------------+---------------------+
        
        nova secgroup-add-rule gr_seguridad tcp 22 22 0.0.0.0/0
        +-------------+-----------+---------+-----------+--------------+
        | IP Protocol | From Port | To Port | IP Range  | Source Group |
        +-------------+-----------+---------+-----------+--------------+
        | tcp         | 22        | 22      | 0.0.0.0/0 |              |
        +-------------+-----------+---------+-----------+--------------+

3. Obtenemos la información de las imágenes, sabores (tipos de
servidores) y redes que tenemos disponibles.

        nova image-list
        +--------------------------------------+--------------------------------------------------------+--------+--------+
        | ID                                   | Name                                                   | Status | Server |
        +--------------------------------------+--------------------------------------------------------+--------+--------+
        | 13e3a912-a0b6-4c6e-adeb-f05331a54e0a | CentOS 6.4 Minimal - 64 bits                           | ACTIVE |        |
        | 85da1e36-4720-424d-a88d-1e27a423b5ab | CentOS 6.5 Minimal - 64 bits                           | ACTIVE |        |
        | d7dead23-3b7f-4b81-8d6f-f5816a71b054 | CentOS 7.0 Minimal - 64 bits                           | ACTIVE |        |
        | ae329732-ba7c-47e5-b083-aa4c1099bf31 | CoreOS 367.1.0 - STABLE                                | ACTIVE |        |
        | 956294f7-1681-4583-b316-0a4f4cc13368 | Debian 7 (Wheezy) Stable - 64 bits                     | ACTIVE |        |
        | 1ca0ee93-3481-4da3-a2d1-8538683f0a07 | Debian 8 (Jessie) Testing - 64 bits                    | ACTIVE |        |
        | 22c6bd09-290d-4fc2-ae6e-41a190d42f73 | Fedora 19 Cloud - 64 bits                              | ACTIVE |        |
        | 9e5220ce-1d87-4b57-aea6-b2bf85aaa665 | Fedora 20 Cloud - 64 bits                              | ACTIVE |        |
        | daf5f9fd-a2de-4308-b9d4-8b99c8cf77bd | Fedora 21 Cloud - 64 bits                              | ACTIVE |        |
        | 89c36ffc-94c0-4804-b7e6-27882ee5f045 | FreeBSD 10.0 Cloudimage - 64 bits                      | ACTIVE |        |
        | 1885f10b-a71f-48c1-a2b3-72453aaf5a65 | FreeBSD 9.2 Cloudimage - 64 bits                       | ACTIVE |        |
        | 56370234-bee0-4670-bdfb-7cff7a7e61c9 | Minecraft Server 1.8.3 based on Ubuntu 14.04 - 64bits  | ACTIVE |        |
        | 4daa5361-5a16-44bc-8714-e990f169a826 | Ubuntu 12.04 LTS (Precise Pangolin) Server - 64 bits   | ACTIVE |        |
        | c4d67783-10a9-428d-9e5e-1beb5ab05222 | Ubuntu 13.04 (Raring Ringtail) Server - 64 bits        | ACTIVE |        |
        | e210bfa5-99c1-4aed-b7a1-8468bdbbe2b7 | Ubuntu 13.10 (Saucy Salamander) Server - 64 bits       | ACTIVE |        |
        | b1c15672-a7e5-466a-b4f1-89a49a364f84 | Ubuntu 14.04 LTS (Trusty Tahr) Beta 2 Server - 64 bits | ACTIVE |        |
        | 44288012-b805-455f-a21f-74ab36c46362 | Ubuntu 14.04 LTS - Trusty Tahr - 64 bits               | ACTIVE |        |
        | 4273e6d3-5944-457c-9396-d87946e763f6 | Ubuntu 14.10 - Utopic Unicorn                          | ACTIVE |        |
        | 2dde6e4b-413a-4cbe-8470-6e4c9f04ffe3 | Windows-2012R2-EVAL-64-bits                            | ACTIVE |        |
        | cc5a5fdb-b5d5-40d8-a8a4-42a0205f35a8 | cirros 0.3.3 x86                                       | ACTIVE |        |
        +--------------------------------------+--------------------------------------------------------+--------+--------+
        
        nova flavor-list
        +----+-------------------+-----------+------+-----------+------+-------+-------------+-----------+
        | ID | Name              | Memory_MB | Disk | Ephemeral | Swap | VCPUs | RXTX_Factor | Is_Public |
        +----+-------------------+-----------+------+-----------+------+-------+-------------+-----------+
        | 1  | m1.nano           | 256       | 0    | 0         |      | 1     | 1.0         | True      |
        | 2  | m1.tiny           | 512       | 0    | 0         |      | 1     | 1.0         | True      |
        | 3  | m1.small          | 1024      | 10   | 0         |      | 1     | 1.0         | True      |
        | 4  | m1.small.windows  | 1024      | 20   | 10        |      | 1     | 1.0         | True      |
        | 5  | m1.medium         | 2048      | 20   | 0         |      | 2     | 1.0         | True      |
        | 6  | m1.medium.windows | 2048      | 20   | 20        |      | 2     | 1.0         | True      |
        | 7  | m1.large          | 4096      | 20   | 0         |      | 2     | 1.0         | True      |
        | 8  | m1.micronano      | 64        | 0    | 0         |      | 1     | 1.0         | True      |
        | 9  | m1.micro          | 128       | 0    | 0         |      | 1     | 1.0         | True      |
        +----+-------------------+-----------+------+-----------+------+-------+-------------+-----------+


        nova net-list
        +--------------------------------------+----------+------+
        | ID                                   | Label    | CIDR |
        +--------------------------------------+----------+------+
        | 82e61988-bf6b-414e-a326-088a839c5347 | red_demo | -    |
        | a86fc437-de50-4034-8e1a-f37a7b05537f | ext-net  | -    |
        +--------------------------------------+----------+------+

4. Ya podemos crear nuestra instancia. Vamos a crear una instancia de
nombre *instancia_nova* con la imagen de Ubuntu 14.04 LTS, un sabor
m1.small, el grupo de  seguridad y las claves ssh que hemos creado
anteriormente y conectado a la red 00000335-net (la contrabarra "\" se
utiliza para poder escribir una instrucción tan larga en varias
líneas). 

        nova boot --flavor m1.small \
        --image 44288012-b805-455f-a21f-74ab36c46362 \
        --security-groups gr_seguridad \
        --key-name clave-acceso \
        --nic net-id=fb4b596f-c603-4d95-ad4e-b68204df76ca \
        instancia_nova  

        +--------------------------------------+---------------------------------------------------------------------------------+
        | Property                             | Value                                                                           |
        +--------------------------------------+---------------------------------------------------------------------------------+
        | OS-DCF:diskConfig                    | MANUAL                                                                          |
        | OS-EXT-AZ:availability_zone          | nova                                                                            |
        | OS-EXT-STS:power_state               | 0                                                                               |
        | OS-EXT-STS:task_state                | scheduling                                                                      |
        | OS-EXT-STS:vm_state                  | building                                                                        |
        | OS-SRV-USG:launched_at               | -                                                                               |
        | OS-SRV-USG:terminated_at             | -                                                                               |
        | accessIPv4                           |                                                                                 |
        | accessIPv6                           |                                                                                 |
        | adminPass                            | 7CQGQS9b63oB                                                                    |
        | config_drive                         |                                                                                 |
        | created                              | 2015-04-22T11:58:14Z                                                            |
        | flavor                               | m1.small (3)                                                                  |
        | hostId                               |                                                                                 |
        | id                                   | b6fc4b18-8c24-4099-b97e-8d5e799982a8                                            |
        | image                                | Ubuntu 14.04 LTS - Trusty Tahr - 64 bits (44288012-b805-455f-a21f-74ab36c46362) |
        | key_name                             | clave-acceso                                                                    |
        | metadata                             | {}                                                                              |
        | name                                 | instancia_nova                                                                  |
        | os-extended-volumes:volumes_attached | []                                                                              |
        | progress                             | 0                                                                               |
        | security_groups                      | gr_seguridad                                                                    |
        | status                               | BUILD                                                                           |
        | tenant_id                            | 1a0b324cc09c40c79286fc1bc63c5833                                                |
        | updated                              | 2015-04-22T11:58:14Z                                                            |
        | user_id                              | 054c20e0a4ab427bbdaf9086db62ec31                                                |
        +--------------------------------------+---------------------------------------------------------------------------------+

5. Por último reservamos una nueva ip flotante del pool "ext-net" y la
asignamos a la instancia para poder acceder a ella desde el exterior:

        nova floating-ip-pool-list
        +---------+
        | name    |
        +---------+
        | ext-net |
        +---------+ 

        nova floating-ip-create ext-net
        +----------------+-----------+----------+---------+
        | Ip             | Server Id | Fixed Ip | Pool    |
        +----------------+-----------+----------+---------+
        | 172.22.204.144 | -         | -        | ext-net |
        +----------------+-----------+----------+---------+
  

        nova floating-ip-associate instancia_nova 172.22.204.144

6. Ya podemos acceder a la instancia:

        ssh -i ~/.ssh/clave-acceso.pem ubuntu@172.22.204.144
        Welcome to Ubuntu 14.04 LTS (GNU/Linux 3.13.0-24-generic x86_64)		  

        * Documentation:  https://help.ubuntu.com/		    

        System information as of Thu Oct 23 07:37:00 UTC 2014		 

        System load: 0.95              Memory usage: 5%   Processes:       69
        Usage of /:  55.3% of 1.32GB   Swap usage:   0%   Users logged in: 0		  

        Graph this data and manage this system at:
        https://landscape.canonical.com/		  

        Get cloud support with Ubuntu Advantage Cloud Guest:
        http://www.ubuntu.com/business/services/cloud		 

        0 packages can be updated.
        0 updates are security updates.		
    	
    	   

        The programs included with the Ubuntu system are free software;
        the exact distribution terms for each program are described in the
        individual files in /usr/share/doc/*/copyright.		   

        Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
        applicable law.		   

        ubuntu@instancia-nova:~$ 

### Resumen de instrucciones

#### Parámetros

    #Listar de imágenes
    nova image-list		

    # Listar sabores
    nova flavor-list		

    # Listar las redes que tenemos definidas
    nova net-list		

    # Listar grupos de seguridad
    nova secgroup-list	

    # Listar reglas de un grupo de seguridad
    nova secgroup-list-rules default		

    # Listar claves ssh
    nova keypair-list

#### Instancias

    # Crear una instancia
    nova boot --flavor FLAVOR_ID --image IMAGE_ID \
    --security-groups SEC_GROUP --key-name KEY_NAME \
    --nic net-id=NET_ID \
    INSTANCE_NAME

    # Pausar instancia
    nova pause INSTANCE_NAME 
    nova unpause INSTANCE_NAME 		

    # Suspender/reanudar
    nova suspend INSTANCE_NAME
    nova resume INSTANCE_NAME		

    # Reiniciar
    nova reboot --hard SERVER		

    # Terminar
    nova delete INSTANCE_NAME		

#### IPs flotantes

     # Listar el pool de ip flotantes
     nova floating-ip-pool-list		

     # Listar las ip flotantes asignadas al proyecto
     nova floating-ip-list

     # Asignar una IP flotante al proyecto
     nova floating-ip-create	

     # Liberar una IP flotante
     nova floating-ip-delete X.X.X.X

     # Asociar una IP flotante a una instancia
     nova floating-ip-associate instancia_prueba X.X.X.X

     # Desasociar una IP flotante de una instancia
     nova floating-ip-disassociate instancia_prueba X.X.X.X

#### Grupos de seguridad

    # Listar grupos de seguridad
    nova secgroup-list		

    # Crear un grupo de seguridad
    nova secgroup-create cortafuegos 'Descripción'		

    # Listar reglas de un grupo de seguridad
    nova secgroup-list-rules default		

    # Añadir una regla a un grupo de seguridad
    nova secgroup-add-rule default tcp 22 22 0.0.0.0/0		

#### Claves ssh

    # Crear un par de claves ssh
    nova keypair-add mi_claves > mi_claves.pem

#### Instantáneas

    # Crear una instantánea de una instancia
    nova image-create --poll instancia_prueba snapshot_prueba		

    # Listar instantáneas (e imágenes)
    nova image-list		

Y la instrucción *más importante de todas*:

    nova help