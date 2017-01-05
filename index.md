# NoChat ![NoChat](https://github.com/okynos/ProyectoCC/blob/master/resources/images/logo.png?raw=true)

[![License](https://img.shields.io/aur/license/yaourt.svg?style=plastic)](https://github.com/okynos/ProyectoCC/blob/master/LICENSE)
[![Status](https://img.shields.io/badge/Status-documenting-yellow.svg)](https://github.com/okynos/ProyectoCC/blob/master/README.md)
[![Language](https://img.shields.io/badge/language-NodeJS-green.svg)](https://nodejs.org/es/)
[![Language](https://img.shields.io/badge/language-Python-green.svg)](https://www.python.org/)
[![Language](https://img.shields.io/badge/language-Go-green.svg)](https://golang.org/)
#### José Luis Fernández Aguilera - Cloud Computing

#### ¡Novedades! 
* Orquestación

## Indice
* Resumen
* Arquitectura
* Desarrollo
* Provisionamiento
* Orquestación

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


### Orquestación
Para orquestación de máquinas virtuales se ha utilizado la herramienta de orquestación configuración y en algunos momentos provisionamiento llamada vagrant la cual permite lanzar instancias de una misma imagen o box como se denominan a las imágenes ya preparadas para ser utilizadas con vagrant, en este caso hemos realizado orquestación de tres formas diferentes que pasamos a detallar pero antes es necesario explicar los pasos que se requieren para instalar vagrant en nuestra máquina anfitrión.

Para instalar vagrant se pued erecurrir al repositorio oficial de vuestra distribución que es posible que contenga una versión un tanto antigua(1.8.1) en mi caso a si que podemos descargar la última versión desde la [página de vagrant](https://www.vagrantup.com/downloads.html) de nuevo en mi caso la instalación de la última versión no me ha funcionado correctamente por lo tanto yo recomiendo la 1.8.7, podemos seguir estos comandos para descargarla e instalarla:

```
wget https://releases.hashicorp.com/vagrant/1.8.7/vagrant_1.8.7_x86_64.deb
sudo dpkg -i vagrant_1.8.7_x86_64.deb
sudo apt-get install ruby
```

una vez tenemos vagrant disponible pasamos a la parte de orquestación.


##### Orquestación vagrant y virtualbox
En este caso vamos a orquestar máquinas virtuales en nuestra propia máquina, para ello necesitamos instalar algún proveedor de virtualización como es el caso de virtualbox, al igual que con vagrant es necesario instalarlo por lo que nos dirigimos a la [página de virtualbox](https://www.virtualbox.org/) o nos lo descargamos e instalamos con los siguientes comandos(Ubuntu 16.04 x64):

```
wget http://download.virtualbox.org/virtualbox/5.1.10/virtualbox-5.1_5.1.10-112026~Ubuntu~xenial_amd64.deb
sudo dpkg -i virtualbox-5.1_5.1.10-112026~Ubuntu~xenial_amd64.deb
```

En algunos tipos de virtualizaciones nos puede ser útil el pack de extensiones de virtualbox lo podemos instalar así:

```
wget http://download.virtualbox.org/virtualbox/5.1.10/Oracle_VM_VirtualBox_Extension_Pack-5.1.10-112026.vbox-extpack
sudo VBoxManage extpack install Oracle_VM_VirtualBox_Extension_Pack-5.1.10-112026.vbox-extpack
```

Ahora necesitamos una imagen de un sistema operativo para lanzarlo a través de virtualbox, para ello ejecutamos el comando:

```
vagrant box add https://atlas.hashicorp.com/ARTACK/boxes/debian-jessie
```
Nos descargará una imagen de Debian Jessie llamada en este caso ARTACK/debian-jessie ahora la inicializamos y lanzamos con:

```
vagrant init ARTACK/debian-jessie
vagrant up
vagrant ssh
```
De esta forma utilizando varios directorios podemos lanzar las máquinas que necesitemos orquestar y conectarnos a ellas.


##### Orquestación vagrant y libvirt
En este caso vamos a orquestar máquinas virtuales de la misma manera que con virtualbox, en una máquina local pero en vez de utilizar virtualbox, vamos a utilizar una libreria llamada libvirt la cual se encarga de adaptar las instrucciones de vagrant a cualquiera de los sistemas de virtualización basados en linux como qemu(KVM) o lxc para realizar orquestación con libvirt solo es necesario seguir los siguientes pasos:

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
Esto nos descargará una distro llamada viniciusfs/centos6`la inicializamos y lanzamos.

```
vagrant init viniciusfs/centos6
vagrant up --provider=libvirt
vagrant ssh
```
Es necesario especificar el proveedor.

##### Orquestación vagrant y OpenStack
Finalmente solo nos queda la orquestación más compleja orquestación en un servidor remoto, en nuestro caso vamos a utilizar la plataforma gratuita trystack la cual nos permite orquestar hasta 3 máquinas virtuales aunque con algunas limitaciones.

En un primer paso ya deberíamos tener configurado tanto vagrant como todas las dependencias que fueran necesarias por lo tanto nos saltamos algunos pasos y ejecutamos:

```
sudo vagrant plugin install vagrant-openstack-provider
```
Esto nos instalará el plugin de openstack para vagrant, una vez instalado nos dirigimos a un directorio vacío y creamos un Vagrantfile con el siguiente contenido para conectar con trystack:

```
Vagrant.configure(2) do |config|

  # Specify the default SSH username and private key
  config.ssh.username = "NOMBRE_DE_USUARIO_EN_LA_IMAGEN"
  config.ssh.private_key_path = "PATH_AL_FICHERO_PEM"

  # Configure the OpenStack provider for Vagrant
  config.vm.provider "openstack" do |os|

    # Specify OpenStack authentication information
    os.openstack_auth_url = "http://DIRECCIÓN_SERVIDOR_OPENSTACK:5000/v2.0"
    os.username = "USUARIO_OPENSTACK"
    os.password = "CLAVE_OPENSTACK"
    os.tenant_name = "NOMBRE_DEL_PROYECTO"

    # Specify instance information
    os.server_name = "NOMBRE_DE_LA_MÁQUINA_A_CREAR"
    os.flavor = "m1.smaller"
    os.image = "NOMBRE_DE_LA_IMAGEN_EN_OPENSTACK"
    os.floating_ip_pool = "public"
    os.networks = "RED_OPENSTACK"
    os.keypair_name = "NOMBRE_DEL_FICHERO_PEM"
    os.security_groups = ["default"]
  end
end
```

Será necesario que modifiques el fichero con la configuración propia que Openstack o Trystack proporciona.
Para finalizar solo será necesario lanzar vagrant y se creará la máquina en el servidor remoto.

```
sudo vagrant up
ansible-playbook scriptAnsible.yml --private-key PATH_A_FICHERO_PEM
```

Y si queremos destruir la instancia solo tenemos que hacer:
```
sudo vagrant destroy
```


##### Provisionamiento y orquestación
Un último paso necesario tras la orquestación es el provisionamiento, el cual en este caso se realiza a través del propio vagrant con ejecuciones shell pero eso no es práctico ya que las imágenes pueden variar su intérprete por lo tanto es mucho más cómodo provisionar como hasta ahora lo hemos hecho en nuestro caso con Rex o Ansible los cuales son capaces de provisionar las máquinas orquestadas con vagrant de una manera sencilla tras crear una instancia con vagrant solo es necesario llamar a Rex o a Ansible con una orden indicandole la dirección que tiene que provisionar de esta manera podemos orquestar y provisionar multitud de máquinas de manera muy rápida.

Un ejemplo para provisionar una máquina instanciada con vagrant y provisionada con Ansible es el siguiente:

```
sudo vagrant up
ansible-playbook scriptAnsible.yml --private-key ~/keys/keypair.pem
```

En caso que deseemos provisionar la máquina de openstack al mismo tiempo que la creamos es necesario añadir este código al vagrantfile
```
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "scriptAnsible.yml"
    ansible.limit = "all"
    ansible.raw_arguments = ["-i" + floating_ip + ","]
  end
```
Dentro de la zona de config de vagrant.

Y modificar ligeramente el script cambiando la línea hosts para que provisiones todas las máquinas que se le pasen como parámetro.
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

Finalmente para provisionar la máquina ejecutamos el comando:
```
sudo vagrant provision
```

En el caso de querer lanzar y provisionar varias máquinas al mismo tiempo en OpenStack es posible pero no en la cuenta limitada de Trystack, en la sección de orquestación de este proyecto puedes encontrarlo, aún así te lo enlazo [aquí](https://github.com/okynos/ProyectoCC/blob/master/orquestacion/Vagrantfile)

Para ello es necesario definir varias máquinas en el vagrantfile y provisionarlas con ansible, el problema de trystack es que solo proporciona una IP por lo que solo podremos provisionar una sola máquina al mismo tiempo sin cambiar esa IP.

Demostración
[Captura OpenStack](https://raw.githubusercontent.com/okynos/ProyectoCC/gh-pages/images/Captura_openstack.png)
[Captura Provisionamiento](https://raw.githubusercontent.com/okynos/ProyectoCC/gh-pages/images/capturaManuel.png)

### Correcciones

* Añadido la documentación a través de una rama gh-pages. -> hito 0
* Corregidos los issues asociados al hito 0 para que los issues se cierren con commit. -> hito 0
* Añadida más documentación en este mismo fichero. -> hito 0
* Añadida arquitectura y breve explicación del desarrollo. -> hito 1
* Añadido procedimiento de provisionamiento. -> hito 2
* Añadida aclaración de los microservicios. -> hito 2
