# Actividad 5: Pipeline DevOps con Make y Bash

## Resumen del entorno
* **SO** Ubuntu 24.04.3 (WSL2)

## Parte 1 Construir

### Ejercicio 1: Análisis del make help

El comando `make help` imprime una lista de los targets disponibles, extrayebndo los comentarios '##' del propio Makefile. La directiva `.DEFAULT_GOAL := help` establece que, si se ejecuta make sin argumentos, la tarea por defecto será mostrar esta ayuda. Finalmente, declarar targets como `.PHONY` asegura que make siempre ejecute sus targets, incluso si existe un archivo con el mismo nombre, tratándolos como comandos lógicos en lugar de archivos.

### Ejercicio 2: Idempotencia de build

En la primera corrida, make detectó que el objetivo out/hello.txt no existía, por lo que ejecutó los comandos para crearlo. En la segunda corrida, make consultó su grafo de dependencias y comparó las marcas de tiempo: vio que out/hello.txt ya existía y era más reciente que su dependencia src/hello.py. Al ver que el target estaba actualizado, make no ejecutó ninguna acción, mostrando que realiza compilaciones incrementales.

### Ejercicio 3: Modo estricto shell y .DELETE_ON_ERROR

El modo estricto del shell (-e) asegura que falle de inmediato cuando el comando python4 no se encuentra, reportando un error a make en lugar de continuar. Al detectar este error, make activa la regla DELETE_ON_ERROR, que elimina el archivo objetivo que se estaba generando (out/hello.txt). Esto grantiza que no queden artefactos corruptos o vacíos tras una compilación fallida.

### Ejercicio 4: 

El resultado de make -n es una simulación que muestra los comandos que se ejecutarían para construir el targett, pero sin ejecutarlos. Por otro lado, make -d expone el razonamiento interno de make paso a paso.

### Ejercicio 5:

Cuando ejecuto touch src/hello.py, se actualiza la marca de tiempo del archivo fuente. make ve que la dependencia es más nueva que el out (out/hello.txt) y concluye que está obsoleto, por lo que lo reconstruye. Cuando ejecuto touch out/hello.txt, hace que el objetivo sea el archivo más reciente. make ve que ninguna de sus dependencias es más nueva y asume que ya está actualizado.

### Ejercicio 6:

Como run_tests.sh aún no está creado, se muestra un error  de shellcheck (SC2148) significa que no puede encontrar la línea "shebang" al inicio del archivo, por lo que no sabe qué tipo de shell analizar. La salida de shfmt es una sugerencia de formato para añadir una línea en blanco a un archivo vacío.

### Ejercicio 7:

El hash SHA256 obtenido es:

06dcdc5678fab6c785ce1a36457821735e42741c3c986606bfe84b58554d1541  dist/app.tar.gz

La opción --sort=name ordena los archivos alfabéticamente dentro del tar, mientras que --mtime='@0', --owner=0, --group=0 y --numeric-owner fijan los metadatos del archivo (fecha de modificación, propietario y grupo) a valores constantes. Finalmente, la opción -n de gzip omite la fecha del archivo original en la cabecera del comprimido. Con esto se asegura que el SHA256 sea idéntico en cada ejecución.

### Ejercicio 8:

Make exige TAB al inicio de las líneas de una receta como una regla de sintaxis estricta para diferenciar los comandos a ejecutar de otras directivas.

La forma más rápida de diagnosticarlo es prestando atención al mensaje de error *** missing separator, que indica el archivo y el número de línea exactos del problema, y en un editor de codigo se debería activar la opción par "mostrar caracteres de espacio en balnco" esto mostrará si la linea comienza con espacios en lugar de una flecha.



