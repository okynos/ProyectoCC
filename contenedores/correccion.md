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

