# Respuestas a las Preguntas de la Actividad 6

### 1. ¿Cómo te ha ayudado Git a mantener un historial claro y organizado de tus cambios?

Al convertir cada conjunto de cambios en un commit. Cada commit funciona como un punto de guardado con un identificador único y un mensaje descriptivo. 

---
### 2. ¿Qué beneficios ves en el uso de ramas para desarrollar nuevas características o corregir errores?

El principal beneficio de las ramas es que permiten crear un entorno de trabajo separado del código principal. Esto significa que se puede experimentar, desarrollar nuevas funcionalidades o corregir errores sin riesgo de desestabilizar la versión funcional. Además varios desarrolladores pueden trabajar simultáneamente sin interferir entre sí, integrando los cambios solo cuando están listos.

---
### 3. Realiza una revisión final del historial de commits para asegurarte de que todos los cambios se han registrado correctamente.

Al ejecutar `git log --oneline --graph`, se pudo verificar que el historial refleja con precisión todas las operaciones realizadas. 

Salida (Fragmentada):

```bash
ffd963f (HEAD -> master) Agrega main.py
931a47f Configura la documentación base del repositorio
7b885e7 Commit Inicial con README.md
e3bd11f Parte inicial hasta el git status
91457e4 (origin/master, origin/HEAD) Finalizada Actividad 5 README

```

---
### 4. Revisa el uso de ramas y merges para ver cómo Git maneja múltiples líneas de desarrollo.

Al crear una rama, se generó un historial paralelo donde se pudo trabajar de forma independiente. El comando `git merge` fue el encargado de fusionar ese historial de vuelta a la rama principal. 

