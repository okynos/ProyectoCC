# Corrección del script de orquestación + provisionamiento(Ansible) - José Luis Fernández Aguilera

### Nombre del alumno
Manuel Blanco Rienda

### Nombre del script
Vagrantfile

### Errores encontrados
No se han encontrado errores aparentes.

### Comentarios
El script de vagrant está bien configurado para funcionar en un proveedor de máquinas virtuales diferente a Trystack, ya éste no permite
la orquestación de varias instancias al proveer una única ip flotante. Sin embargo está bien que se dé el script para realizar dicha tarea en
otro proveedor, ya que es lo más normal hoy en día en vez de orquestar de una en una las instancias.

### Documentación
A continuación se muestran las dos capturas tomadas que demuestran la veracidad de que ha sido ejecutado el script por Manuel Blanco Rienda y que
 se verfica que es correcto: [Captura 1](https://github.com/manuelbr/Proyecto_CC/blob/gh-pages/images/jose3_1.png) [Captura 2](https://github.com/manuelbr/Proyecto_CC/blob/gh-pages/images/jose3_2.png)

---

### Nombre del alumno
Jose Manuel Moya Baena

### Nombre del script
Vagrantfile

### Errores encontrados
No se han encontrado errores aparentes.

### Comentarios
He logrado hacer funcionar la orquestacion de la maquina con centOS, pero no la debian. El problema quiza resida en la version de *vagrant* o *virtualbox* que estoy usando, ya que el anterior compañero las ha logrado ejecutar correctamente.

Aun asi, he analizado el codigo en los archivos Vagrantfile y todo parece correcto.

### Documentación
A continuacion se muestran las capturas con las ejecuciones de ambos script [centOS](https://github.com/jose999/PlayAnywhere/blob/master/provisionamiento/imagenes/correccion/centOS.png) [Debian](https://github.com/jose999/PlayAnywhere/blob/master/provisionamiento/imagenes/correccion/debian)
