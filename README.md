# NoChat ![NoChat](https://github.com/okynos/ProyectoCC/blob/master/resources/images/logo.png?raw=true)

[![License](https://img.shields.io/aur/license/yaourt.svg?style=plastic)](https://github.com/okynos/ProyectoCC/blob/master/LICENSE)
[![Status](https://img.shields.io/badge/Status-documenting-yellow.svg)](https://github.com/okynos/ProyectoCC/blob/master/README.md)
[![Language](https://img.shields.io/badge/language-NodeJS-green.svg)](https://nodejs.org/es/)
[![Language](https://img.shields.io/badge/language-Python-green.svg)](https://www.python.org/)
[![Language](https://img.shields.io/badge/language-Go-green.svg)](https://golang.org/)
#### Jos� Luis Fern�ndez Aguilera - Cloud Computing

#### �Novedades! 
* Provisionamiento
* Documentaci�n sobre servicios (corregida falta hito 1)

## Documentaci�n.
La documentaci�n del proyecto se divide en varios subapartados en los cuales se detallan los distintos aspectos de la aplicaci�n que se va a desarrollar.

### Breve resumen
Este proyecto se basa en una aplicaci�n web que permite a los usuarios chatear entre s� mediante sockets de la libreria socket.io con conexi�n y desconexi�n en tiempo real a la web y todo ello con nodejs.
Este proyecto variar� en funci�n de lo aprendido en la asignatura de Cloud Computing incluso llegando a la posibilidad de cambiar totalmente el tipo de proyecto a desarrollar para centrarse m�s en la arquitectura de micro servicios m�s id�nea para despliegue en la nube facilitando as� la escalabilidad del sistema.
Dividiendo el proyecto entre una aplicaci�n que realiza las tareas de administraci�n y gesti�n de usuarios y los servicios de los que dispone que no tienen porque encontrarse en la misma m�quina y ni siquiera utilizar el mismo lenguaje o ser programadas por mi mismo puede ser de terceros gracias a la utilizaci�n del protocolo REST.

### Arquitectura
La arquitectura de la aplicaci�n se basa en una arquitectura de microservicios, en la cual existe un controlador central el cual utilizar� un sistema de colas llamado RabbitMQ, que gestionar� la llegada de eventos a la aplicaci�n y llamar� a los microservicios necesarios para realizar el evento.

En nuestra arquitectura contaremos con 6 microservicios propios:
- Microservicio de logeo propio con MongoDB para loguear todos los sucesos de la aplicaci�n.
- Microservicio de gesti�n de sockets e interconexi�n entre usuarios.
- Microservicio de notificaciones.
- Microservicio de gesti�n del almacenamiento de usuarios utilizando una base de datos relacional.
- Microservicio de presentaci�n y gesti�n (interfaz).

Tambi�n se utilizar�n unos microservicios externos:
- Microservicio de logging de datos del servidor externo a nuestros microservicios.
- Microservicio de gesti�n del almacenamiento redundante de datos externo.

Dados todos los microservicios enumerados se puede presuponer que la estructura se basar� en una aplicaci�n que har� de gestor/controlador e ir� llamando a cada uno de los microservicios que se necesiten en un momento dado. 

### Desarrollo
Esta aplicaci�n que actuar� de controlador se programar� en NodeJS (Javascript) y utilizar� el servicio de RabbitMQ para gestionar las colas de eventos que se produzcan.

En cuanto a los lenguajes que se utilizar�n en los microservicios no est� bien definido y se escoger�n varios en funci�n de las capacidades o el que mejor se adapte a la tarea que se ha descrito en el inicio del readme, algunos posibles lenguajes son:
- JavaScript o NodeJS
- Python
- Go (para aprender)
- Java
- Otros.

Cada microservicio puede tener un lenguaje independientemente de los dem�s es por ello que no se especifica ning�n lenguaje concreto.

Para comenzar este proyecto se parte de una base ya realizada de un chat en NodeJS pero de estructura monol�tica que se adaptar� a la arquitectura de microservicios separando y modularizando el c�digo necesario.

### Provisionamiento
Para desplegar esta aplicaci�n son necesarios una serie de aplicaciones de tipo servidor, que la mayor�a de distribuciones no incorporan en su imagen base, es por ello que vamos a realizar un provisionamiento de los paquetes b�sicos, que necesitar�n luego nuestras aplicaciones en las m�quinas desplegadas en la red, para ello podemos utilizar varios lenguajes de provisionamiento de m�quinas como pueden ser Chef, Salt, Rex, Puppet, Ansible y alguno m�s que no he nombrado pero en nuestro caso vamos a realizarlo en dos sistemas diferentes, Ansible y Rex.

##### Provisionando con Ansible
Ansible es una aplicaci�n de provisionamiento que permite la instalaci�n de paquetes a trav�s de una consola python la cual puede ejecutar cualquier tipo de instrucci�n que se define en un lenguaje de descripci�n llamado en este caso YAML.

Para empezar a provisionar en Ansible es necesario asegurarse que la m�quina que queremos provisionar cuenta con una instalaci�n de python2.7 dado que es la versi�n que utiliza, si queremos instalar m�dulos de python ser� necesario tambien el paquete python-pip.

inicialmente es necesario intalar en la m�quina que provisiona/anfitri�n unos m�dulo python que utilizar� ansible y el propio ansible con el comando:

```
sudo pip install paramiko PyYAML jinja2 httplib2 ansible
```

Una vez se hayan instalado todas las dependencias es mejor para nuestro dise�o crearnos un archivo de hosts el cual Ansible utilizar� para provisionar las m�quinas que definamos en �l y lo exportamos como variable de entorno.

```
echo "[NOMBRE GRUPO M�QUINAS]" > ~/ansible_hosts
echo "IP O DIRECCI�N DE LA M�QUINA" >> ~/ansible_hosts
export ANSIBLE_HOSTS=~/ansible_hosts
```

Con esto ya tendr�amos toda la configuraci�n de la parte de ansible realizada, s�lo necesitamos lanzar el script con la siguiente orden y observar la salida que nos va dando, los scripts de provisionamiento se pueden encontrar [aqu�](https://github.com/okynos/ProyectoCC/tree/master/provisionamiento):

```
ansible-playbook scriptAnsible.yml --private-key keypair.pem
```

Donde keypair es vuestra clave de acceso a la m�quina por ssh, si ten�is copiada una clave p�blica en la m�quina remota no ser�a necesario este par�metro, es porsible que tengais que cambiar el nombre de usuario en el script o ponerlo por l�nea de �rdenes con la opci�n -u para conectaros con el usuario de vuestra m�quina remota.


##### Provisionando con Rex
En este caso para instalar Rex s�lo es necesario instalar el paquete llamado rex desde l�nea de comandos en Ubuntu, en otros casos ser� necesario ejecutar la siguiente orden:

```
curl -L https://get.rexify.org | perl - --sudo -n Rex
```

Esto descargar� e instalar� Rex en nuestro equipo, una vez hecho esto ya podemos empezar a escribir un script de provisionamiento.

Rex utiliza un lenguaje descriptivo al igual que otras aplicaciones el script de provisionamiento que se ha creado est� [aqu�](https://github.com/okynos/ProyectoCC/tree/master/provisionamiento):


Este script se debe llamar Rexfile y est� dividido en dos tareas dependiendo del tipo de servicio que queramos provisionar en nuestro caso son "deployServers" para instalar todos los servidores (RabbitMQ, Mysql, Mongo) y "deployNode" para instalar NodeJs y todas las dependencias requeridas.

Para ejecutar este script s�lo es necesario lanzar la siguiente orden en el directorio donde se encuentre el fichero Rexfile:

```
rex deployServers
rex deplouNode
```

Dependiendo del servicio que queramos provisionar lanzaremos un comando u otro para instalar los paquetes que se hayan codificado en el Rexfile.


### Correcciones

* A�adido la documentaci�n a trav�s de una rama gh-pages. -> hito 0
* Corregidos los issues asociados al hito 0 para que los issues se cierren con commit. -> hito 0
* A�adida m�s documentaci�n en este mismo fichero. -> hito 0
* A�adida arquitectura y breve explicaci�n del desarrollo. -> hito 1
* A�adido procedimiento de provisionamiento. -> hito 2
* A�adida aclaraci�n de los microservicios. -> hito 2
