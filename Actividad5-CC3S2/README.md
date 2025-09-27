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

---

## Parte 2 LEER

### Análisis con make -n y make -d

La ejecución de make -n all me permitió observar la secuencia completa de comandos que se ejecutarían (tools, lint, build, test, package) sin ejecutarlos, mostrando la expansión de variables como $< y $@. Por otro lado, make -d build reveló el razonamiento de make: analizó las marcas de tiempo, vio que el objetivo out/hello.txt no existía o era más antiguo que src/hello.py, y por eso decidió que "Debe rehacerse".

### Analisis de make tools en un entorno BSD tar en PATH

Al simular un entorno con BSD tar, la verificación make tools falló correctamente, demostrando la importancia de la dependencia de GNU tar. Las opciones --sort=name, --numeric-owner y --mtime son imprescindibles para la reproducibilidad determinista, ya que eliminan metadatos variables como el orden de los archivos, los nombres de usuario/grupo y las fechas de modificación. 

### Análisis make verify-repro

La salida es esta:

```bash
SHA256_1=9094ca603d4b10a7ecd1673a1ead91c6e6d32adbaa79b0a264f373a0db1f6d3f
SHA256_2=9094ca603d4b10a7ecd1673a1ead91c6e6d32adbaa79b0a264f373a0db1f6d3f
OK: reproducible
```
Esto confirma que el pipeline de build es reproduible.

La ejecución de PYTHON=python3.12 make test demuestra cómo el pipeline puede adaptarse a diferentes versiones de herramientas. En el Makefile la asignación PYTHON ?= python3 es condicional. Como la variable PYTHON ya fue definida en el entorno (python3.12) al ejecutar el comando.

El objetivo make test ejecuta las pruebas en secuencia: primero scripts/run_tests.sh y, solo si este tiene éxito, python -m unittest. Si el primer script falla, la receta se detiene inmediatamente gracias al modo estricto del shell. 

Al ejecutar touch src/hello.py, su marca de tiempo se actualiza. Cuando se invoca make all, como src/hello.py es más reciente que out/hello.txt, el objetivo build se rehace. A su vez, como out/hello.txt es una dependencia de package, este también se reconstruye. El objetivo test también se ejecuta porque depende directamente de la fuente. 

El comando make -j4 all instruye a make para ejecutar hasta 4 trabajos en paralelo. Se evitan condiciones de carrera gracias a dos factores: dependencias precisas y recetas seguras, como el uso de mkdir -p $(@D), que crea un directorio sin fallar si ya existe. 

make lint ejecuta shellcheck, shfmt -d y ruff para analizar el código y reportar errores o inconsistencias sin modificar los archivos. Por otro lado, make format es un corrector automático que usa herramientas como shfmt -w para reescribir los archivos y ajustarlos al estilo definido en el proyecto.

---

## Parte 3 EXTENDER

### 3.1 lint mejorado

 Al romper intencionadamente el _quoting_ de una variable en `run_tests.sh`, `make lint` falló inmediatamente, con `shellcheck` reportando el error esperado (SC2086). Tras corregirlo, el `lint` pasó correctamente. `make format` aplicó el estilo de código estándar.

 ### 3.2 Rollback adicional

Se modificó el script para simular la desaparición de un archivo temporal a mitad de ejecución. El script detectó la anomalía, mostró un mensaje de error claro y terminó con un código de salida específico (`3`).

### 3.3 Incrementalidad

Las mediciones con `make benchmark` confirmaron el comportamiento esperado. La primera ejecución fue lenta (compilación completa), la segunda fue instantánea (todo estaba cacheado) y la tercera, tras `touch src/hello.py`, volvió a ser lenta porque `make` detectó el cambio y reconstruyó todos los objetivos dependientes (`build`, `test`, `package`).