# NoChat ![NoChat](https://github.com/okynos/ProyectoCC/blob/master/resources/images/logo.png?raw=true)

[![License](https://img.shields.io/aur/license/yaourt.svg?style=plastic)](https://github.com/okynos/ProyectoCC/blob/master/LICENSE)
[![Status](https://img.shields.io/badge/Status-documenting-yellow.svg)](https://github.com/okynos/ProyectoCC/blob/master/README.md)
[![Language](https://img.shields.io/badge/language-NodeJS-green.svg)](https://nodejs.org/es/)
[![Language](https://img.shields.io/badge/language-Python-green.svg)](https://www.python.org/)
[![Language](https://img.shields.io/badge/language-Go-green.svg)](https://golang.org/)
#### Jos� Luis Fern�ndez Aguilera - Cloud Computing

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
- Microservicio de monitorizaci�n de la carga del sistema en momentos puntuales.
- Microservicio de notificaciones.
- Microservicio de almacenamiento de usuarios con base de datos relacional.
- Microservicio de presentaci�n (interfaz).

Tambi�n se utilizar�n unos microservicios externos:
- Microservicio de logueo externo a nuestros microservicios.
- Microservicio de Almacenamiento de datos externo.

Dados todos los microservicios enumerados se puede presuponer que la estructura se basar� en una aplicaci�n que har� de gestor/controlador e ir� llamando a cada uno de los microservicios que se necesiten en un momento dado. 

### Desarrollo
Esta aplicaci�n que actuar� de controlador se programar� en NodeJS (Javascript) y usando  RabbitMQ para gestionar las colas de eventos que se produzcan.

En cuanto a los microservicios el lenguaje que se utilizar� no est� bien definido y se escoger� uno en funci�n de las capacidades o el que mejor se adapte a la tarea se ha descrito en el inio del readme algunos posibles lenguajes que son:
- JavaScript o NodeJS
- Python
- Go (para aprender)
- Java
- Otros.

Cada microservicio puede tener un lenguaje independientemente de los dem�s es por ello que no se especifica ning�n lenguaje concreto.

Para comenzar este proyecto se parte de una base ya realizada de un chat en NodeJS pero de estructura monol�tica que se adaptar� a la arquitectura de microservicios separando y modularizando el c�digo necesario.

### Correcciones

* A�adido la documentaci�n a trav�s de una rama gh-pages.
* Corregidos los issues asociados al hito 0 para que los issues se cierren con commit.
* A�adida m�s documentaci�n en este mismo fichero.
* A�adida arquitectura y breve explicaci�n del desarrollo.
