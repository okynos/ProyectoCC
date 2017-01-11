# Corrección del Dockerfile

### Nombre del alumno
Manuel Blanco Rienda

### Nombre del script
Dockerfile, DockerfileMySQL y DockerfileNodeJS

### Errores encontrados
No se han encontrado errores aparentes en los dos primeros scripts, sin embargo en el último aparecen dos warnings relacionados con el repositorio origen desde donde se instala NodeJS, aunque esto no tiene relación con el autor del script y NodeJS se instala correctamente sin fallos, por lo que no es nada a tener en cuenta.

### Comentarios
Se ha podido crear el contenedor de forma adecuada a partir de cada script, utilizando una distribución muy ligera de alpine, lo cuál es un acierto por el poco peso que supone. Sin embargo hay que tener en cuenta que alpine utiliza un gestor de paquetes diferente a las principales distribuciones de linux y que podría presentar ciertos problemas con determinados paquetes. En cualquier caso, es una distribución adecuada para estas prácticas.

### Documentación

Se adjuntan las capturas que se obtuvieron al crear cada contenedor a partir de cada Dockerfile:

* Dockerfile (Primero) = [captura 1](https://github.com/manuelbr/Proyecto_CC/blob/gh-pages/images/jose1.png) y [captura 2](https://github.com/manuelbr/Proyecto_CC/blob/gh-pages/images/jose2.png).

* DockerfileMySQL (Segundo) = [captura 1](https://github.com/manuelbr/Proyecto_CC/blob/gh-pages/images/jose3.png).

* DockerfileNodeJS (Tercero) = [captura 1](https://github.com/manuelbr/Proyecto_CC/blob/gh-pages/images/jose4.png), [captura 2](https://github.com/manuelbr/Proyecto_CC/blob/gh-pages/images/jose5.png), [captura 3](https://github.com/manuelbr/Proyecto_CC/blob/gh-pages/images/jose6.png) y [captura 4](https://github.com/manuelbr/Proyecto_CC/blob/gh-pages/images/jose7.png).

---

### Nombre del alumno
José Manuel Moya Baena

### Nombre del script
Dockerfile, DockerfileMySQL y DockerfileNodeJS

### Errores encontrados
No se ha encontrado ningun error referente a los scripts. Como cita la correccion anterior, se muestran unos waning que no afectan para nada en la instalacion de NodeJS ya que la creacion del contenedor acaba satisfactoriamente.

### Comentarios
Se ha podido crear el contenedor de forma adecuada a partir de cada script, utilizando una distribución muy ligera de alpine, lo cuál es un acierto por el poco peso que supone. Sin embargo hay que tener en cuenta que alpine utiliza un gestor de paquetes diferente a las principales distribuciones de linux y que podría presentar ciertos problemas con determinados paquetes. En cualquier caso, es una distribución adecuada para estas prácticas.

Para probar los distintos contenedores ha sido necesario renombar los distintos archivos, ya que Docker a la hora de construir el contenedor busca el archivo "DockerFile" en la ruta que le demos como argumento. Quiza hubiera sido mejor subir todos los contenedores a DockerHub para poder probarlos evitando este paso.

### Documentación

Se adjuntan las capturas que se obtuvieron al crear cada contenedor a partir de cada Dockerfile:

* Dockerfile (Git, Curl Python) = [captura 1](https://github.com/jose999/PlayAnywhere/blob/master/contenedores/imagenes/DockerFile.png).

* DockerfileMySQL (Git, Curl, MySQL) = [captura 1](https://github.com/jose999/PlayAnywhere/blob/master/contenedores/imagenes/DockerHubMySQL.png).

* DockerfileNodeJS (Git, Curl, NodeJS, Express, Socket.IO) = [captura 1](https://github.com/jose999/PlayAnywhere/blob/master/contenedores/imagenes/DockerFile%20NodeJS1.png), [captura 2](https://github.com/jose999/PlayAnywhere/blob/master/contenedores/imagenes/DockerFile%20NodeJS2.png).
