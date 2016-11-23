# NoChat ![NoChat](https://github.com/okynos/ProyectoCC/blob/master/resources/images/logo.png?raw=true)

[![License](https://img.shields.io/aur/license/yaourt.svg?style=plastic)](https://github.com/okynos/ProyectoCC/blob/master/LICENSE)
[![Status](https://img.shields.io/badge/Status-documenting-yellow.svg)](https://github.com/okynos/ProyectoCC/blob/master/README.md)
[![Language](https://img.shields.io/badge/language-NodeJS-green.svg)](https://nodejs.org/es/)
[![Language](https://img.shields.io/badge/language-Python-green.svg)](https://www.python.org/)
[![Language](https://img.shields.io/badge/language-Go-green.svg)](https://golang.org/)
#### José Luis Fernández Aguilera - Cloud Computing

#### ¡Novedades! 
* Provisionamiento
* Documentación sobre servicios (corregida falta hito 1)

## Documentación.
La documentación del proyecto se divide en varios subapartados en los cuales se detallan los distintos aspectos de la aplicación que se va a desarrollar.

### Breve resumen
Este proyecto se basa en una aplicación web que permite a los usuarios chatear entre sí mediante sockets de la libreria socket.io con conexión y desconexión en tiempo real a la web y todo ello con nodejs.
Este proyecto variará en función de lo aprendido en la asignatura de Cloud Computing incluso llegando a la posibilidad de cambiar totalmente el tipo de proyecto a desarrollar para centrarse más en la arquitectura de micro servicios más idónea para despliegue en la nube facilitando así la escalabilidad del sistema.
Dividiendo el proyecto entre una aplicación que realiza las tareas de administración y gestión de usuarios y los servicios de los que dispone que no tienen porque encontrarse en la misma máquina y ni siquiera utilizar el mismo lenguaje o ser programadas por mi mismo puede ser de terceros gracias a la utilización del protocolo REST.

### Arquitectura
La arquitectura de la aplicación se basa en una arquitectura de microservicios, en la cual existe un controlador central el cual utilizará un sistema de colas llamado RabbitMQ, que gestionará la llegada de eventos a la aplicación y llamará a los microservicios necesarios para realizar el evento.

En nuestra arquitectura contaremos con 6 microservicios propios:
- Microservicio de logeo propio con MongoDB para loguear todos los sucesos de la aplicación.
- Microservicio de gestión de sockets e interconexión entre usuarios.
- Microservicio de notificaciones.
- Microservicio de gestión del almacenamiento de usuarios utilizando una base de datos relacional.
- Microservicio de presentación y gestión (interfaz).

También se utilizarán unos microservicios externos:
- Microservicio de logging de datos del servidor externo a nuestros microservicios.
- Microservicio de gestión del almacenamiento redundante de datos externo.

Dados todos los microservicios enumerados se puede presuponer que la estructura se basará en una aplicación que hará de gestor/controlador e irá llamando a cada uno de los microservicios que se necesiten en un momento dado. 

### Desarrollo
Esta aplicación que actuará de controlador se programará en NodeJS (Javascript) y utilizará el servicio de RabbitMQ para gestionar las colas de eventos que se produzcan.

En cuanto a los lenguajes que se utilizarán en los microservicios no está bien definido y se escogerán varios en función de las capacidades o el que mejor se adapte a la tarea que se ha descrito en el inicio del readme, algunos posibles lenguajes son:
- JavaScript o NodeJS
- Python
- Go (para aprender)
- Java
- Otros.

Cada microservicio puede tener un lenguaje independientemente de los demás es por ello que no se especifica ningún lenguaje concreto.

Para comenzar este proyecto se parte de una base ya realizada de un chat en NodeJS pero de estructura monolítica que se adaptará a la arquitectura de microservicios separando y modularizando el código necesario.

### Provisionamiento
Para desplegar esta aplicación son necesarios una serie de aplicaciones de tipo servidor, que la mayoría de distribuciones no incorporan en su imagen base, es por ello que vamos a realizar un provisionamiento de los paquetes básicos, que necesitarán luego nuestras aplicaciones en las máquinas desplegadas en la red, para ello podemos utilizar varios lenguajes de provisionamiento de máquinas como pueden ser Chef, Salt, Rex, Puppet, Ansible y alguno más que no he nombrado pero en nuestro caso vamos a realizarlo en dos sistemas diferentes, Ansible y Rex.

##### Provisionando con Ansible
Ansible es una aplicación de provisionamiento que permite la instalación de paquetes a través de una consola python la cual puede ejecutar cualquier tipo de instrucción que se define en un lenguaje de descripción llamado en este caso YAML.

Para empezar a provisionar en Ansible es necesario asegurarse que la máquina que queremos provisionar cuenta con una instalación de python2.7 dado que es la versión que utiliza, si queremos instalar módulos de python será necesario tambien el paquete python-pip.

inicialmente es necesario intalar en la máquina que provisiona/anfitrión unos módulo python que utilizará ansible y el propio ansible con el comando:

```
sudo pip install paramiko PyYAML jinja2 httplib2 ansible
```

Una vez se hayan instalado todas las dependencias es mejor para nuestro diseño crearnos un archivo de hosts el cual Ansible utilizará para provisionar las máquinas que definamos en él y lo exportamos como variable de entorno.

```
echo "[NOMBRE GRUPO MÁQUINAS]" > ~/ansible_hosts
echo "IP O DIRECCIÓN DE LA MÁQUINA" >> ~/ansible_hosts
export ANSIBLE_HOSTS=~/ansible_hosts
```

Con esto ya tendríamos toda la configuración de la parte de ansible realizada, sólo necesitamos lanzar el script con la siguiente orden y observar la salida que nos va dando, los scripts de provisionamiento se pueden encontrar [aquí](https://github.com/okynos/ProyectoCC/tree/master/provisionamiento):

```
ansible-playbook scriptAnsible.yml --private-key keypair.pem
```

Donde keypair es vuestra clave de acceso a la máquina por ssh, si tenéis copiada una clave pública en la máquina remota no sería necesario este parámetro, es porsible que tengais que cambiar el nombre de usuario en el script o ponerlo por línea de órdenes con la opción -u para conectaros con el usuario de vuestra máquina remota.


##### Provisionando con Rex
En este caso para instalar Rex sólo es necesario instalar el paquete llamado rex desde línea de comandos en Ubuntu, en otros casos será necesario ejecutar la siguiente orden:

```
curl -L https://get.rexify.org | perl - --sudo -n Rex
```

Esto descargará e instalará Rex en nuestro equipo, una vez hecho esto ya podemos empezar a escribir un script de provisionamiento.

Rex utiliza un lenguaje descriptivo al igual que otras aplicaciones el script de provisionamiento que se ha creado está [aquí](https://github.com/okynos/ProyectoCC/tree/master/provisionamiento):


Este script se debe llamar Rexfile y está dividido en dos tareas dependiendo del tipo de servicio que queramos provisionar en nuestro caso son "deployServers" para instalar todos los servidores (RabbitMQ, Mysql, Mongo) y "deployNode" para instalar NodeJs y todas las dependencias requeridas.

Para ejecutar este script sólo es necesario lanzar la siguiente orden en el directorio donde se encuentre el fichero Rexfile:

```
rex deployServers
rex deplouNode
```

Dependiendo del servicio que queramos provisionar lanzaremos un comando u otro para instalar los paquetes que se hayan codificado en el Rexfile.


### Correcciones

* Añadido la documentación a través de una rama gh-pages. -> hito 0
* Corregidos los issues asociados al hito 0 para que los issues se cierren con commit. -> hito 0
* Añadida más documentación en este mismo fichero. -> hito 0
* Añadida arquitectura y breve explicación del desarrollo. -> hito 1
* Añadido procedimiento de provisionamiento. -> hito 2
* Añadida aclaración de los microservicios. -> hito 2
