---
layout: blog
tittle: Curso OpenStack
menu:
  - Unidades
---

###Otras opciones para cinder

LVM+iSCSI es una solución válida para entornos de prueba o pequeños
entornos donde no se necesita utilizar gran cantidad de volúmenes y
este enfoque tradicional puede ser una solución sencilla y fácil de
implementar, a la vez que proporciona la estabilidad de una tecnología
muy extendida y conocida.

Sin embargo, en despliegues mayores como son en muchos casos los
utilizados por empresas que implantan OpenStack, como una solución
genérica para un centro de datos, otras opciones pueden resultar más
adecuadas.

### Ceph RBD

[Ceph](http://http://ceph.com/) es un proyecto de software libre que
proporciona una solución única de almacenamiento para diferentes
necesidades de una organización. Utilizando el mismo clúster de
máquinas Ceph que garantiza la replicación y disponibilidad de
cualquier objeto almacenado en él, es posible disponer de:

* Dispositivos de bloques en red, mediante el componente Ceph RBD
* Almacenamientos de objetos con APIs compatibles con Amazon S3 y
OpenStack Swift a través del componente Ceph Object Gateway
* Sistema de ficheros distribuidos a través del componente CephFS

Ceph no requiere la utilización de caros dispositivos de
almacenamiento dedicados, sino que es una solución libre completamente
basada es software y que es posible implementar en hardware
convencional, lo que está convirtiéndola en la opción de
almacenamiento predilecta en muchos despliegues.

El hecho de que Ceph pueda proporcionar tanto dispositivos de bloques
en red para cinder como almacenamiento de objetos con una API
compatible con Swift, hace que sea la opción predilecta para
implementar nubes en entornos de producción con OpenStack, tal como
aparece reflejado de forma sistemática en las encuestas de uso de
OpenStack que se publican semestralmente.

### Dispositivos de hardware

Otra opción disponible en cinder es utilizar dispositivos de hardware
específicos para proporcionar el almacenamiento de dispositivos de
bloques, en un enfoque más clásico en el que se cuenta con equipos
específicos en la SAN para tal fin (controladores, cabinas de disco,
etc.)

Está opción puede ser la más adecuada cuando se quiera utilizar
hardware del que ya se dispone, se pueda obtener mayor rendimiento en
determinados casos o porque haya ciertas funcionalidades
que sean precisas que aún no estén soportadas por las opciones de
software.

Un aspecto muy importante a considerar a la hora de utilizar
dispositivos de hardware para el almacenamiento de bloques es
verificar el nivel de compatibilidad y funcionalidad que pueden
proporcionar a cinder:

[CinderSupportMatrix](https://wiki.openstack.org/wiki/CinderSupportMatrix)