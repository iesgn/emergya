---
layout: blog
tittle: Curso OpenStack
menu:
  - Unidades
---

## Gestión de instancias con OpenStack client (OVS)

Vamos a ver todos los pasos que necesitamos para crear una instancia: 

1. Solicitamos a nova que cree un nuevo par de claves ssh (que
redirigimos al fichero clave-acceso.pem) y modificamos los permisos de
la clave privada de forma adecuada:

        openstack keypair create clave_demo2 > clave_demo2.pem
        chmod 400 ~/.ssh/clave_demo2.pem

2. Creamos un nuevo grupo seguridad (cortafuegos) y creamos una regla
que nos permita el acceso por ssh. 

        openstack security group create gr_seguridad
        +-------------+--------------------------------------+
        | Field       | Value                                |
        +-------------+--------------------------------------+
        | description | gr_seguridad                         |
        | id          | 0c9d15c1-6b13-43ff-a973-56c6ecf3ef7e |
        | name        | gr_seguridad                         |
        | rules       | []                                   |
        | tenant_id   | e2661319dea4407cbc9370ecf995d1ca     |
        +-------------+--------------------------------------+

        
        openstack security group rule create --proto tcp --src-ip 0.0.0.0/0 --dst-port 22 gr_seguridad
        +-----------------+--------------------------------------+
        | Field           | Value                                |
        +-----------------+--------------------------------------+
        | group           | {}                                   |
        | id              | 77b24d67-f7ef-41b4-823c-037d90568550 |
        | ip_protocol     | tcp                                  |
        | ip_range        | 0.0.0.0/0                            |
        | parent_group_id | 0c9d15c1-6b13-43ff-a973-56c6ecf3ef7e |
        | port_range      | 22:22                                |
        +-----------------+--------------------------------------+


3. Obtenemos la información de las imágenes, sabores (tipos de
servidores) y redes que tenemos disponibles.

        openstack image list
        +--------------------------------------+---------------------------------+
        | ID                                   | Name                            |
        +--------------------------------------+---------------------------------+
        | 30e0af35-6cd4-4d9e-a372-606bf3deea0a | cirros-0.3.4-x86_64-uec         |
        | b8017624-cbe4-460b-9e0c-ad0908420e1e | cirros-0.3.4-x86_64-uec-ramdisk |
        | ac76a3cb-6425-4188-ac12-dc823e7c91f9 | cirros-0.3.4-x86_64-uec-kernel  |
        +--------------------------------------+---------------------------------+
      
        
        openstack flavor list
        +----+-----------+-------+------+-----------+-------+-----------+
        | ID | Name      |   RAM | Disk | Ephemeral | VCPUs | Is Public |
        +----+-----------+-------+------+-----------+-------+-----------+
        | 1  | m1.tiny   |   512 |    1 |         0 |     1 | True      |
        | 2  | m1.small  |  2048 |   20 |         0 |     1 | True      |
        | 3  | m1.medium |  4096 |   40 |         0 |     2 | True      |
        | 4  | m1.large  |  8192 |   80 |         0 |     4 | True      |
        | 5  | m1.xlarge | 16384 |  160 |         0 |     8 | True      |
        +----+-----------+-------+------+-----------+-------+-----------+

        openstack network list
        +--------------------------------------+---------+--------------------------------------+
        | ID                                   | Name    | Subnets                              |
        +--------------------------------------+---------+--------------------------------------+
        | 0fcc0de8-ca9f-4740-8722-233ec3eb2a1b | public  | c6a34bab-699e-4ba5-9daf-357ae6a0480f |
        | ef06b714-8b3a-4af4-bca0-d0eae224c0b9 | private | a629ff78-b057-4b18-b501-7d5ee788b6dd |
        +--------------------------------------+---------+--------------------------------------+


4. Ya podemos crear nuestra instancia. Vamos a crear una instancia de
nombre *instancia_nova* con una de las imágenes disponibles, un sabor
m1.tiny, el grupo de  seguridad y las claves ssh que hemos creado
anteriormente y conectado a la red interna (la contrabarra "\" se
utiliza para poder escribir una instrucción tan larga en varias
líneas). 

        openstack server create --flavor m1.tiny \
        --image cirros-0.3.4-x86_64-uec \
        --security-group gr_seguridad \
        --key-name clave_demo2 \
        --nic net-id=ef06b714-8b3a-4af4-bca0-d0eae224c0b9 \
        instancia2

        +--------------------------------------+----------------------------------------------------------------+
        | Field                                | Value                                                          |
        +--------------------------------------+----------------------------------------------------------------+
        | OS-DCF:diskConfig                    | MANUAL                                                         |
        | OS-EXT-AZ:availability_zone          | nova                                                           |
        | OS-EXT-STS:power_state               | 0                                                              |
        | OS-EXT-STS:task_state                | scheduling                                                     |
        | OS-EXT-STS:vm_state                  | building                                                       |
        | OS-SRV-USG:launched_at               | None                                                           |
        | OS-SRV-USG:terminated_at             | None                                                           |
        | accessIPv4                           |                                                                |
        | accessIPv6                           |                                                                |
        | addresses                            |                                                                |
        | adminPass                            | 7N6tB6kdRfrb                                                   |
        | config_drive                         |                                                                |
        | created                              | 2016-01-02T13:50:46Z                                           |
        | flavor                               | m1.tiny (1)                                                    |
        | hostId                               |                                                                |
        | id                                   | 3b1da7d4-932b-420f-a916-6d1df0170008                           |
        | image                                | cirros-0.3.4-x86_64-uec (30e0af35-6cd4-4d9e-a372-606bf3deea0a) |
        | key_name                             | clave_demo2                                                    |
        | name                                 | instancia2                                                     |
        | os-extended-volumes:volumes_attached | []                                                             |
        | progress                             | 0                                                              |
        | project_id                           | e2661319dea4407cbc9370ecf995d1ca                               |
        | properties                           |                                                                |
        | security_groups                      | [{u'name': u'gr_seguridad'}]                                   |
        | status                               | BUILD                                                          |
        | updated                              | 2016-01-02T13:50:46Z                                           |
        | user_id                              | f219b0ebb4d34a249ec7b9310f55e980                               |
        +--------------------------------------+----------------------------------------------------------------+


5. Por último reservamos una nueva ip flotante del pool "ext-net" y la
asignamos a la instancia para poder acceder a ella desde el exterior:

        $ openstack ip floating pool list
        +--------+
        | Name   |
        +--------+
        | public |
        +--------+


         openstack ip floating create public
        +-------------+--------------------------------------+
        | Field       | Value                                |
        +-------------+--------------------------------------+
        | fixed_ip    | None                                 |
        | id          | cf829fb5-e9d3-42de-9d10-d06f531326f5 |
        | instance_id | None                                 |
        | ip          | 172.24.4.4                           |
        | pool        | public                               |
        +-------------+--------------------------------------+

  
        openstack ip floating add 172.24.4.4 instancia2


6. Ya podemos acceder a la instancia:

        ssh -i clave_demo2.pem cirros@172.24.4.4
        $ 


###Documentación

* [OpenStackClient keypair](http://docs.openstack.org/developer/python-openstackclient/command-objects/keypair.html)
* [OpenStackClient security group](http://docs.openstack.org/developer/python-openstackclient/command-objects/security-group.html)
* [OpenStackClient security group rule](http://docs.openstack.org/developer/python-openstackclient/command-objects/security-group-rule.html)
* [OpenStackClient flavor](http://docs.openstack.org/developer/python-openstackclient/command-objects/flavor.html)
* [OpenStackClient network](http://docs.openstack.org/developer/python-openstackclient/command-objects/network.html)
* [OpenStackClient ip floating](http://docs.openstack.org/developer/python-openstackclient/command-objects/ip-floating.html)