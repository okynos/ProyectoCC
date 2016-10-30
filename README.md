# NoChat ![NoChat](https://github.com/okynos/ProyectoCC/blob/master/resources/images/logo.png?raw=true)

[![License](https://img.shields.io/aur/license/yaourt.svg?style=plastic)](https://github.com/okynos/ProyectoCC/blob/master/LICENSE)
[![Status](https://img.shields.io/badge/Status-documenting-yellow.svg)](https://github.com/okynos/ProyectoCC/blob/master/README.md)
[![Language](https://img.shields.io/badge/language-NodeJS-green.svg)](https://nodejs.org/es/)
[![Language](https://img.shields.io/badge/language-Python-green.svg)](https://www.python.org/)
[![Language](https://img.shields.io/badge/language-Go-green.svg)](https://golang.org/)
#### José Luis Fernández Aguilera - Cloud Computing

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
- Microservicio de monitorización de la carga del sistema en momentos puntuales.
- Microservicio de notificaciones.
- Microservicio de almacenamiento de usuarios con base de datos relacional.
- Microservicio de presentación (interfaz).

También se utilizarán unos microservicios externos:
- Microservicio de logueo externo a nuestros microservicios.
- Microservicio de Almacenamiento de datos externo.

Dados todos los microservicios enumerados se puede presuponer que la estructura se basará en una aplicación que hará de gestor/controlador e irá llamando a cada uno de los microservicios que se necesiten en un momento dado. 

### Desarrollo
Esta aplicación que actuará de controlador se programará en NodeJS (Javascript) y usando  RabbitMQ para gestionar las colas de eventos que se produzcan.

En cuanto a los microservicios el lenguaje que se utilizará no está bien definido y se escogerá uno en función de las capacidades o el que mejor se adapte a la tarea se ha descrito en el inio del readme algunos posibles lenguajes que son:
- JavaScript o NodeJS
- Python
- Go (para aprender)
- Java
- Otros.

Cada microservicio puede tener un lenguaje independientemente de los demás es por ello que no se especifica ningún lenguaje concreto.

Para comenzar este proyecto se parte de una base ya realizada de un chat en NodeJS pero de estructura monolítica que se adaptará a la arquitectura de microservicios separando y modularizando el código necesario.

### Correcciones

* Añadido la documentación a través de una rama gh-pages.
* Corregidos los issues asociados al hito 0 para que los issues se cierren con commit.
* Añadida más documentación en este mismo fichero.
* Añadida arquitectura y breve explicación del desarrollo.
