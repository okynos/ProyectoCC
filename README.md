# NoChat ![NoChat](https://github.com/okynos/ProyectoCC/blob/master/resources/images/logo.png?raw=true)

[![License](https://img.shields.io/aur/license/yaourt.svg?style=plastic)](https://github.com/okynos/ProyectoCC/blob/master/LICENSE)
[![Status](https://img.shields.io/badge/Status-documenting-yellow.svg)](https://github.com/okynos/ProyectoCC/blob/master/README.md)
[![Language](https://img.shields.io/badge/language-NodeJS-green.svg)](https://nodejs.org/es/)
[![Language](https://img.shields.io/badge/language-Python-green.svg)](https://www.python.org/)
[![Language](https://img.shields.io/badge/language-Go-green.svg)](https://golang.org/)
#### Jos� Luis Fern�ndez Aguilera - Cloud Computing

#### �Novedades! 
* Orquestaci�n

## Indice
* Resumen
* Arquitectura
* Desarrollo
* Provisionamiento
* Orquestaci�n

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

### Correcciones

* A�adido la documentaci�n a trav�s de una rama gh-pages. -> hito 0
* Corregidos los issues asociados al hito 0 para que los issues se cierren con commit. -> hito 0
* A�adida m�s documentaci�n en este mismo fichero. -> hito 0
* A�adida arquitectura y breve explicaci�n del desarrollo. -> hito 1
* A�adido procedimiento de provisionamiento. -> hito 2
* A�adida aclaraci�n de los microservicios. -> hito 2
