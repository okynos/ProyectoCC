# NoChat ![NoChat](https://github.com/okynos/ProyectoCC/blob/master/resources/images/logo.png?raw=true)

[![License](https://img.shields.io/aur/license/yaourt.svg?style=plastic)](https://github.com/okynos/ProyectoCC/blob/master/LICENSE)
[![Status](https://img.shields.io/badge/Status-documenting-yellow.svg)](https://github.com/okynos/ProyectoCC/blob/master/README.md)
[![Language](https://img.shields.io/badge/language-NodeJS-green.svg)](https://nodejs.org/es/)
[![Language](https://img.shields.io/badge/language-Python-green.svg)](https://www.python.org/)
[![Language](https://img.shields.io/badge/language-Go-green.svg)](https://golang.org/)
#### Jos� Luis Fern�ndez Aguilera - Cloud Computing



#### �Novedades! 
* Despliegue final



## Indice
* Resumen
* Arquitectura
* Desarrollo
* Provisionamiento
* Orquestaci�n
* Contenedores
* Despliegue final
* Correcciones



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
rex deployNode
```

Dependiendo del servicio que queramos provisionar lanzaremos un comando u otro para instalar los paquetes que se hayan codificado en el Rexfile.



### Orquestaci�n
Para orquestaci�n de m�quinas virtuales se ha utilizado la herramienta de orquestaci�n configuraci�n y en algunos momentos provisionamiento llamada vagrant la cual permite lanzar instancias de una misma imagen o box como se denominan a las im�genes ya preparadas para ser utilizadas con vagrant, en este caso hemos realizado orquestaci�n de tres formas diferentes que pasamos a detallar pero antes es necesario explicar los pasos que se requieren para instalar vagrant en nuestra m�quina anfitri�n.

Para instalar vagrant se pued erecurrir al repositorio oficial de vuestra distribuci�n que es posible que contenga una versi�n un tanto antigua(1.8.1) en mi caso a si que podemos descargar la �ltima versi�n desde la [p�gina de vagrant](https://www.vagrantup.com/downloads.html) de nuevo en mi caso la instalaci�n de la �ltima versi�n no me ha funcionado correctamente por lo tanto yo recomiendo la 1.8.7, podemos seguir estos comandos para descargarla e instalarla:

```
wget https://releases.hashicorp.com/vagrant/1.8.7/vagrant_1.8.7_x86_64.deb
sudo dpkg -i vagrant_1.8.7_x86_64.deb
sudo apt-get install ruby
```

una vez tenemos vagrant disponible pasamos a la parte de orquestaci�n.


##### Orquestaci�n vagrant y virtualbox
En este caso vamos a orquestar m�quinas virtuales en nuestra propia m�quina, para ello necesitamos instalar alg�n proveedor de virtualizaci�n como es el caso de virtualbox, al igual que con vagrant es necesario instalarlo por lo que nos dirigimos a la [p�gina de virtualbox](https://www.virtualbox.org/) o nos lo descargamos e instalamos con los siguientes comandos(Ubuntu 16.04 x64):

```
wget http://download.virtualbox.org/virtualbox/5.1.10/virtualbox-5.1_5.1.10-112026~Ubuntu~xenial_amd64.deb
sudo dpkg -i virtualbox-5.1_5.1.10-112026~Ubuntu~xenial_amd64.deb
```

En algunos tipos de virtualizaciones nos puede ser �til el pack de extensiones de virtualbox lo podemos instalar as�:

```
wget http://download.virtualbox.org/virtualbox/5.1.10/Oracle_VM_VirtualBox_Extension_Pack-5.1.10-112026.vbox-extpack
sudo VBoxManage extpack install Oracle_VM_VirtualBox_Extension_Pack-5.1.10-112026.vbox-extpack
```

Ahora necesitamos una imagen de un sistema operativo para lanzarlo a trav�s de virtualbox, para ello ejecutamos el comando:

```
vagrant box add https://atlas.hashicorp.com/ARTACK/boxes/debian-jessie
```
Nos descargar� una imagen de Debian Jessie llamada en este caso ARTACK/debian-jessie ahora la inicializamos y lanzamos con:

```
vagrant init ARTACK/debian-jessie
vagrant up
vagrant ssh
```
De esta forma utilizando varios directorios podemos lanzar las m�quinas que necesitemos orquestar y conectarnos a ellas.


##### Orquestaci�n vagrant y libvirt
En este caso vamos a orquestar m�quinas virtuales de la misma manera que con virtualbox, en una m�quina local pero en vez de utilizar virtualbox, vamos a utilizar una libreria llamada libvirt la cual se encarga de adaptar las instrucciones de vagrant a cualquiera de los sistemas de virtualizaci�n basados en linux como qemu(KVM) o lxc para realizar orquestaci�n con libvirt solo es necesario seguir los siguientes pasos:

```
sudo apt-get install qemu libvirt-bin libvirt0 ebtables dnsmasq
sudo apt-get install libxslt1-dev libxml2-dev libvirt-dev zlib1g-dev ruby-dev
sudo vagrant plugin install vagrant-libvirt
gem pristine ruby-libvirt
```
Este proceso nos va a instalar todas las dependencias de libvirt y a instalar el plugin en vagrant ya solo necesitamos configurar la imagen`para probar libvirt vamos a descargar una imagen preparada para virtualizarse con KVM.

```
vagrant box add https://atlas.hashicorp.com/viniciusfs/boxes/centos6/
```
Esto nos descargar� una distro llamada viniciusfs/centos6`la inicializamos y lanzamos.

```
vagrant init viniciusfs/centos6
vagrant up --provider=libvirt
vagrant ssh
```
Es necesario especificar el proveedor.


##### Orquestaci�n vagrant y OpenStack
Finalmente solo nos queda la orquestaci�n m�s compleja orquestaci�n en un servidor remoto, en nuestro caso vamos a utilizar la plataforma gratuita trystack la cual nos permite orquestar hasta 3 m�quinas virtuales aunque con algunas limitaciones.

En un primer paso ya deber�amos tener configurado tanto vagrant como todas las dependencias que fueran necesarias por lo tanto nos saltamos algunos pasos y ejecutamos:

```
sudo vagrant plugin install vagrant-openstack-provider
```
Esto nos instalar� el plugin de openstack para vagrant, una vez instalado nos dirigimos a un directorio vac�o y creamos un Vagrantfile con el siguiente contenido para conectar con trystack:

```
Vagrant.configure(2) do |config|

  # Specify the default SSH username and private key
  config.ssh.username = "NOMBRE_DE_USUARIO_EN_LA_IMAGEN"
  config.ssh.private_key_path = "PATH_AL_FICHERO_PEM"

  # Configure the OpenStack provider for Vagrant
  config.vm.provider "openstack" do |os|

    # Specify OpenStack authentication information
    os.openstack_auth_url = "http://DIRECCI�N_SERVIDOR_OPENSTACK:5000/v2.0"
    os.username = "USUARIO_OPENSTACK"
    os.password = "CLAVE_OPENSTACK"
    os.tenant_name = "NOMBRE_DEL_PROYECTO"

    # Specify instance information
    os.server_name = "NOMBRE_DE_LA_M�QUINA_A_CREAR"
    os.flavor = "m1.smaller"
    os.image = "NOMBRE_DE_LA_IMAGEN_EN_OPENSTACK"
    os.floating_ip_pool = "public"
    os.networks = "RED_OPENSTACK"
    os.keypair_name = "NOMBRE_DEL_FICHERO_PEM"
    os.security_groups = ["default"]
  end
end
```

Ser� necesario que modifiques el fichero con la configuraci�n propia que Openstack o Trystack proporciona.
Para finalizar solo ser� necesario lanzar vagrant y se crear� la m�quina en el servidor remoto.

```
sudo vagrant up
ansible-playbook scriptAnsible.yml --private-key PATH_A_FICHERO_PEM
```

Y si queremos destruir la instancia solo tenemos que hacer:
```
sudo vagrant destroy
```


##### Provisionamiento y orquestaci�n
Un �ltimo paso necesario tras la orquestaci�n es el provisionamiento, el cual en este caso se realiza a trav�s del propio vagrant con ejecuciones shell pero eso no es pr�ctico ya que las im�genes pueden variar su int�rprete por lo tanto es mucho m�s c�modo provisionar como hasta ahora lo hemos hecho en nuestro caso con Rex o Ansible los cuales son capaces de provisionar las m�quinas orquestadas con vagrant de una manera sencilla tras crear una instancia con vagrant solo es necesario llamar a Rex o a Ansible con una orden indicandole la direcci�n que tiene que provisionar de esta manera podemos orquestar y provisionar multitud de m�quinas de manera muy r�pida.

Un ejemplo para provisionar una m�quina instanciada con vagrant y provisionada con Ansible es el siguiente:

```
sudo vagrant up
ansible-playbook scriptAnsible.yml --private-key ~/keys/keypair.pem
```

En caso que deseemos provisionar la m�quina de openstack al mismo tiempo que la creamos es necesario a�adir este c�digo al vagrantfile
```
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "scriptAnsible.yml"
    ansible.limit = "all"
    ansible.raw_arguments = ["-i" + floating_ip + ","]
  end
```
Dentro de la zona de config de vagrant.

Y modificar ligeramente el script cambiando la l�nea hosts para que provisiones todas las m�quinas que se le pasen como par�metro.
```
---
- hosts: all
  become: yes
  remote_user: ubuntu
  tasks:
  - name: Update repo
    apt: 
      update_cache: yes
  - name: Install MongoDB Server
    apt: pkg=mongodb-server state=installed
  - name: Install Mysql Server
    apt: pkg=mysql-server state=installed
  - name: Install RabbitMQ Server
    apt: pkg=rabbitmq-server state=installed
```

Finalmente para provisionar la m�quina ejecutamos el comando:
```
sudo vagrant provision
```

En el caso de querer lanzar y provisionar varias m�quinas al mismo tiempo en OpenStack es posible pero no en la cuenta limitada de Trystack, en la secci�n de orquestaci�n de este proyecto puedes encontrarlo, a�n as� te lo enlazo [aqu�](https://github.com/okynos/ProyectoCC/blob/master/orquestacion/Vagrantfile)

Para ello es necesario definir varias m�quinas en el vagrantfile y provisionarlas con ansible, el problema de trystack es que solo proporciona una IP por lo que solo podremos provisionar una sola m�quina al mismo tiempo sin cambiar esa IP.



### Contenedores
En esta secci�n pasamos a detallar la utilizaci�n por parte del proyecto que estamos desarrollando de los contenedores los cuales son una herramienta muy �til para la prueba y el despliegue de algunos de los microservicios de nuestra aplicaci�n, en un primer paso se explicar� como proceder con la instalaci�n de un entorno de contenedores como es lxc o docker para instalar estos gestores solo es necesario ejecutar el siguiente comando en la terminal:
```
sudo apt-get install lxc
sudo apt-get install docker.io
```

A continuaci�n vamos a crear un contenedor de prueba en local para comprobar el correcto funcionamiento del programa y si queremos subirlo al repositorio de im�genes de docker, [Docker Hub](https://hub.docker.com/), para ello seguimos los siguientes pasos en nuestro caso hemos decidido utilizar una distribuci�n muy utilizada en contenedores por su bajo peso(unos 25 MB) y consumo llamada alpine a la cual le vamos a a�adir algunos paquetes particulares:
```
sudo docker pull alpine
```
Con este comando se nos descargar�a la imagen de alpine y ya podr�amos lanzar un contenedor con la misma, en cambio si lo que queremos es construir nuestro propio contenedor a trav�s de un Dockerfile simplemente es necesario construir el Dockerfile por ejemplo:
```
FROM alpine
MAINTAINER Okynos <pylott@gmail.com>
WORKDIR /root
CMD ["/bin/ash"]

RUN apk update && apk upgrade
RUN apk add git
RUN apk add curl
RUN apk add python
RUN apk add mysql
```
Despu�s de escribir el Dockerfile podemos crear nuestro contenedor personal con el siguiente comando:
```
sudo docker build -t okynos/alpine .
```
Y al finalizar tendr�amos nuestro contenedor creado de manera totalmente autom�tica, si queremos hacer algo m�s en el escribimos:
```
sudo docker run -it okynos/alpine sh
```
Este comando arrancar� el contenedor y permitir� el acceso por consola cediendo el testigo de nuestra shell a la del contenedor, en este paso podemos a�adir paquetes a nuestra distribuci�n tal que as�:
```
apk update
apk upgrade
apk add nodejs
exit
```
Una vez nuestra m�quina tiene todos los programas o servicios que necesitamos salimos de ella, si deseamos guardar este contenedor para poder instanciarlo en cualquier m�quina solo es necesario guardarlo y subirlo a Docker Hub podemos hacerlo as�:
```
sudo docker ps -a
sudo docker start ContainerIDDeLContenedor
sudo docker commit ContainerIDDelContenedor
```

Tras este paso nos logueamos en docker hub y subimos a nuestra cuenta la imagen con el comando:
```
sudo docker login
sudo docker push okynos/alpine
```
Ya estar�a finalizado el proceso de prueba y puesta a punto de nuestros contenedores de manera local y los hemos subido a un repositorio p�blico global para poderlos descargar en la m�quina remota que estemos provisionando y orquestando para a�adirle estos contenedores.

Ahora pasamos a detallar como instanciar los contenedores docker remotamente en Azure para que se realice de manera autom�tica y sencilla, en primer lugar es necesario descargar e instalar docker-machine:
```
sudo curl -L https://github.com/docker/machine/releases/download/v0.8.2/docker-machine-`uname -s`-`uname -m` >~/Descargas/docker-machine
sudo cp docker-machine /usr/local/bin/docker-machine
```
El primer comando descargar� el binario y el segundo lo colocar� en la carpeta de binarios del usuario, continuamos ahora con las �rdenes de docker-machine suponiendo que ya tenemos una cuenta lista en Azure y funcionando como es debido:
```
sudo docker-machine create  --driver azure  --azure-ssh-user "USUARIO"  --azure-subscription-id "ID_SUBCRIPCI�N_AZURE" NOMBRE_DE_LA_M�QUINA_AZURE_EN_MIN�SCULAS
```
Este comando de una forma muy simple nos va a crear todos los elementos necesarios dentro de azure para que exista una m�quina virtual a la que podamos acceder para instanciar nuestros contenedores docker, si nos da alg�n error en los certificados podemos repararlos con el siguiente comando:
```
sudo docker-machine regenerate-certs NOMBRE_DE_LA_M�QUINA_EN_MIN�SCULAS
```

Finalmente podemos acceder a la m�quina a trav�s del siguiente comando o por ssh de manera normal:
```
sudo docker-machine ssh NOMBRE_DE_LA_M�QUINA_EN_MIN�SCULAS
```

Y para finalizar solo necesitamos que dentro de la m�quina de azure ejecutemos un contenedor o varios docker:
```
sudo docker run -it okynos/alpine
```
Que nos descargar� y arrancar� el contenedor que habiamos pusheado al repositorio de Docker Hub.


### Despliegue final
Para el despliegue final hemos escogido utilizar un conjunto de herramientas bastante conocidas por la comunidad y que muchas de ellas ofrecen soporte en le nube directamente, es por ello que nos vamos a ahorrar el desplegar, provisionar y configurar algunos servicios como la base de datos en MongoDB la cual puede requerir de un tiempo que podemos emplear en otros servicios que no se ofertan en la nube de manera gratuita como RabbitMQ.

Para nuestro caso concreto vamos a desplegar una estructura dividida en dos m�quinas virtuales de azure las cuales una contendr� contenedores ligeros de docker que proporcionar�n acceso a la aplicaci�n desarrollada en express, socket.io y NodeJs y otro contenedor que permitir� el acceso a un microservicio que utiliza RabbitMQ, el contenedor de NodeJS lo hemos creado autom�ticamente con un Dockerfile que se puede consultar [aqu�](https://github.com/okynos/ProyectoCC/blob/master/despliegue/Dockerfile) y posteriormente se ha subido a un repositorio de Docker Hub para poder descargarlo en la m�quina azure en la que lo vamos a desplegar, en el caso del contenedor con RabbitMQ hemos observado una imagen bastante liviana de un repositorio externo que ya lo ten�a en un repositorio de Docker Hub y por lo tanto hemos decidido utilizar un trabajo ya realizado dado que para la distribuci�n alpine no existe un paquete preparado en el repositorio.
La otra m�quina de azure se utilizar� para montar una base de datos MariaDB que almacenar� a los usuarios de nuestra aplicaci�n, �sta no ha sido desplegada en un contenedor docker ya que es una base de datos bastante grande y que necesita una base de computaci�n estable por lo tanto se utilizar� la m�quina de azure �ntegra para labores de la base de datos.

Por otro lado se utilizar�n servicios ya desplegados en la nube como MongoLab y logz, los cuales ser�n directamente utilizados desde la aplicaci�n desarrollada que estar� en un contenedor docker de la m�quina de azure que despliega los contenedores docker.
MongoLab se utilizar� para almacenar toda la informaci�n relativa a la actividad de los usuarios, tales como conexiones, desconexiones, chats p�blicos o tendencias de la gente dentro de la aplicaci�n y el servicio de logz se utilizar� para almacenar todos los eventos que se produzcan entre la aplicaci�n desarrollada y los dem�s servicios o con los usuarios directamente, por lo tanto se utilizar� como servicio de logueo en la nube, ambos servicios tienen un plan de pago y un plan gratuito, en este documento solo se har� referencia al plan gratuito de dichos servicios.

Unas breves capturas del despliegue con vagrant se incorporar�n a la documentaci�n en la [web del proyecto](https://okynos.github.io/ProyectoCC/#Correcciones)

Pasamos ahora a detallar todo el proceso realizado para llevar a cabo el despliegue de toda la infraestructura empezando por lo m�s b�sico del proyecto, vamos a detallar desde el principio para que esta parte del documento sirva como gu�a a todo aquel que quiera desplegar la estructura comentada anteriormente o alguna similar.

En primer lugar vamos a crear un par de claves nuevas para utilizarlas con vagrant, para ello abrimos una terminal y ejecutamos el siguiente comando:
```
ssh-keygen -t rsa -b 4096 -C "Tu_Email@Tu_Dominio.com"
```
Le damos un nombre a la llave, si nos pide una clave y s�lo la vamos a usar con vagrant se puede dejar en blanco para que vagrant no est� constantemente pidiendonos la clave de esta llave.

Una vez hemos generado nuestra llave para usarla con vagrant vamos a instalar vagrant 1.8.7 la cual es la versi�n que mejor trabaja con los plugin que existen actualmente (1/02/2017), para ello solo tenemos que dirigirnos a la [p�gina de vagrant](https://www.vagrantup.com/) y descargar el [fichero](https://releases.hashicorp.com/vagrant/1.8.7/) de las releases antiguas que corresponda con nuestra versi�n, en nuestro caso hemos descargado "vagrant_1.8.7_x86_64.deb" tras descargarlo lo podemos instalar con el comando:
```
sudo dpkg -i vagrant_1.8.7_x86_64.deb
```
Una vez se nos haya instalado vagrant correctamente podremos juguetear con el y los archivos Vagrantfile asociados pero primero vamos a instalar el plugin necesario para conectar vagrant y azure, con el siguiente comando lo podremos instalar de una manera sencilla:
```
sudo vagrant plugin install vagrant-azure --plugin-version '2.0.0.pre1' --plugin-prerelease
```
Una vez se nos confirme que se ha instalado el plugin vamos a instalar la parte de ansible para ello debemos instalar python y ansible:
```
sudo apt-get install python python-pip
sudo pip install paramiko PyYAML jinja2 httplib2 ansible
```
En algunos casos y para el que nos ata�e es necesario la versi�n de ansible que nos proporciona nuestra distribuci�n o pip puede no ser suficientemente reciente para manejar los m�dulos de Docker, los cuales se encuentran en la versi�n 2.1 en adelante es por ello que es mejor instalar la �ltima versi�n de ansible de la siguiente forma:
Descargamos la �ltima versi�n del [siguiente enlace](http://releases.ansible.com/ansible/), una vez se ha descargado lo descomprimimos y entramos en el directorio y ejecutamos los siguientes comandos:
```
make
sudo make install
```
Si todo ha funcionado correctamente tendremos ansible en su �ltima version(latest).
Una vez hemos instalado todo esto ya es el momento de meterse en materia en cuanto a la realizaci�n de un vagrantfile que nos cree la estructura que deseamos, un ejemplo funcional de la estructura del vagrantfile es la siguiente:
```
servers=[
  {
    :hostname => "Containers-Host",
    :provision => "scriptContainers.yml"
  },
  {
    :hostname => "Database-Host",
    :provision => "scriptMariaDB.yml"
  }
]

Vagrant.configure('2') do |config|
  config.ssh.username = "vagrant"
  config.ssh.private_key_path = '~/.ssh/id_rsaAzure'
  config.vm.box = 'azure'

  servers.each do |machine|
     config.vm.define machine[:hostname] do |node|

	  config.vm.provider :azure do |azure, override|
	    azure.tenant_id = ENV['AZURE_TENANT_ID']
	    azure.client_id = ENV['AZURE_CLIENT_ID']
	    azure.client_secret = ENV['AZURE_CLIENT_SECRET']
	    azure.subscription_id = ENV['AZURE_SUBSCRIPTION_ID']


	    azure.vm_image_urn = 'credativ:Debian:8:8.0.201701180'
	    azure.vm_size = 'Basic_A0'
	    azure.location = 'westeurope'
	    azure.vm_name = machine[:hostname]
	    azure.tcp_endpoints = '80:80'
	  end
     end
  end

  servers.each do |machine|
     config.vm.provision "ansible" do |ansible|
       ansible.playbook = machine[:provision]
       ansible.sudo = true
     end
  end

end
```
Vamos a detallar el vagrantfile que hemos dispuesto, en este caso el vagrantfile consta de un array llamado servers que contendr� los valores relativos a todos l�s m�quinas Azure que se desean crear, en este caso concreto se tratan de dos m�quina una denominada "Containers-Host", como su nombre indica contendr� contenedores Docker para virtualizaci�n ligera de varias aplicaciones y que se provisionar� con un script de ansible preparado para ello denominado "scriptContainers.yml". Por otra parte tenemos otra m�quina que se encargar� de almacenar la base de datos, esta m�quina se denominar� "Database-Host" en Azure y se provisionar� mediante el script denominado "scriptMariaDB.yml", tras pasar esta parte comienza la parte de configuraci�n de azure la cual se encarga de definir el usuario ssh que se usar�, la clave que subir� a las m�quinas creadas para conectarse por ssh y la caja que utilizar� como base en azure.

Ahora se pasa a la parte del bucle de creaci�n de las instancias en el cual se toman las variables definidas en la llamada a vagrant y se utilizan para crear las dos m�quinas con un plan b�sico y en una distribuci�n de Debian 8 ya que en el �mbito de los servidores suele ser m�s estable y f�cil de instalar y configurar.
Tras este bucle se entra en otro que lo que har� ser� provisionar m�quina a m�quina con los scripts definidos en cada una de las m�quinas.

Una vez se ha detallado el script de vagrant pasamos a los scripts de provisionamiento que vamos a utilizar:
```
---
- hosts: Containers-Host
  become: yes
  remote_user: vagrant

  tasks:
  - name: Update repo
    apt: 
      update_cache: yes

  - name: Install docker.io package
    apt: pkg=docker.io state=latest

  - name: Install python pip
    apt: pkg=python-pip state=latest

  - pip:
      name: docker-py

  - name: Create RabbitMQ container
    docker_container:
      name: rabbit
      image: maryville/rabbitmq
      state: started

  - name: Create NodeJS container
    docker_container:
      name: node
      image: okynos/alpinenode
      state: started
```
En nuestro caso vamos a detallar s�lo el script m�s complejo por no repetir informaci�n dado que el script de MariaDB solo incorpora un par de llamadas a apt que se realizan de la misma manera que en este script, a�n as� el script de MariaDB siempre puede ser consultado [aqu�](https://github.com/okynos/ProyectoCC/blob/master/despliegue/scriptMariaDB.yml)
En el script que hemos dispuesto se puede observar una parte que define los hosts que se provisionar�n con este script dado que solo vamos a crear una instancia para contenedores solo hemos definido un host, definimos el usuario vagrant y pedimos permisos de super usuario para ejecutar algunas instalaciones, tras esto se realiza una actualizaci�n de los repositorios de la distribuci�n debian los cuales pueden venir sin actualizar, tras esto instalamos Docker en la m�quina para poder lanzar las m�quinas Docker y python-pip para poder instalar el complemento necesario por ansible para utilizar docker llamado docker-py, tras instalar todo lo necesario utilizamos un m�dulo de Docker bastante reciente de ansible que nos permite definir y lanzar contenedores dentro de la m�quina que se est� provisionando, y simplemente lanzamos dos contenedores uno que se centrar� en el tratamiento de colas con RabbitMQ y otro que se centrar� en la aplicaci�n que hemos desarrollado en NodeJS.

Una vez tenemos listos todos los scripts para nuestro despliegue lanzamos vagrant de la siguiente forma:
```
sudo AZURE_SUBSCRIPTION_ID=<SUBSCRIPTION_ID> AZURE_CLIENT_ID=<CLIENT_ID> AZURE_CLIENT_SECRET=<CLIENT_SECRET> AZURE_TENANT_ID=<TENANT_ID> vagrant up --provider=azure
```
Debemos completar los campos entre <> para conectarnos con azure, este comando comenzar� a crearnos las m�quinas virtuales y tras desplegarlas nos las va a provisionar mediante los scripts que hemos definido anteriormente, por lo tanto si hemos llegado hasta este punto nuestra red se ha desplegado correctamente y podremos acceder a los servidores que hemos creado en azure con el comando:
```
sudo AZURE_SUBSCRIPTION_ID=<SUBSCRIPTION_ID> AZURE_CLIENT_ID=<CLIENT_ID> AZURE_CLIENT_SECRET=<CLIENT_SECRET> AZURE_TENANT_ID=<TENANT_ID> vagrant ssh <HOSTNAME>
```
En algunos casos puede que Azure no permita el acceso a vagrant para permitirle el acceso ser� necesario entrar en la configuraci�n de la aplicaci�n que hemos definido y con la cual hemos obtenido el id de cliente y permitirle acceso externo desde el propio panel de azure.

En cuanto a los detalles sobre los servicios externos se detallar� m�s y mejor en la [p�gina web del proyecto](https://okynos.github.io/ProyectoCC/) y se a�adir�n algunas capturas del proceso.


### Correcciones

* A�adido la documentaci�n a trav�s de una rama gh-pages. -> hito 0
* Corregidos los issues asociados al hito 0 para que los issues se cierren con commit. -> hito 0
* A�adida m�s documentaci�n en este mismo fichero. -> hito 0
* A�adida arquitectura y breve explicaci�n del desarrollo. -> hito 1
* A�adido procedimiento de provisionamiento. -> hito 2
* A�adida aclaraci�n de los microservicios. -> hito 2
* A�adida documentaci�n de orquestaci�n. -> hito 3
* A�adida documentaci�n de provisionamiento de la orquestaci�n. -> hito 3
* A�adida documentaci�n de contenedores. -> hito 4
* A�adida documentaci�n sobre docker y azure. -> hito 4
* A�adida documentaci�n sobre el despliegue final. -> hito 5
* A�adida documentaci�n sobre vagrant y azure. -> hito 5
* A�adida documentaci�n sobre ansible y docker. -> hito 5
