# Actividad 3 - Parte Teórica: Reflexión Conceptual

---

## 1. Introducción a DevOps: ¿Qué es y qué no es?

DevOps es una metodología que integra los equipos de desarrollo (Dev) y operaciones (Ops) para automatizar y acelerar la entrega de software. Basicamente es una estrategia con un ciclo de vida iterativo y continuo: **Plan -> Code -> Build -> Test -> Release -> Deploy -> Operate**, que es muy diferente al modelo Waterfall, que viene a ser un proceso lineal donde cada fase debe terminar para que la siguiente comience.

El lema "You build it, you run it" es un cambio de cultura. Sginifica que el equipo de desarrollo no solo va a ser responsable de escribir el codigo sino tambien del despliegue, operación y mantenimiento.

### Mitos vs. Realidades

* **Mito**: DevOps es solo un conjunto de herramientas de CI/CD.
    * **Realidad**: Las herramientas son habilitadoras, pero la base es el marco **CALMS (Culture, Automation, Lean, Measurement, Sharing)**. Sin una cultura de colaboración, las herramientas solo automatizan procesos deficientes.
* **Mito**: DevOps es un cargo o un equipo aislado.
    * **Realidad**: Es una mentalidad que impregna toda la organización. Los "Ingenieros DevOps" actúan como facilitadores para que los equipos de desarrollo adopten estas prácticas.

### Gate de Calidad en Makefile

Un gate de calidad es un control automático que bloquea un despliegue si no se cumplen criterios objetivos. 

## 2. MARCO CALMS

C - Culture (Cultura): La colaboración se fomenta al tener un repositorio Git único donde conviven el código, la automatización y la configuración de infraestructura. Todos los equipos trabajan con la misma fuente de verdad.

A - Automation (Automatización): El Makefile es el corazón de la automatización. Orquesta tareas como make deps (instalar dependencias) o make run (ejecutar la app), eliminando la intervención manual y los errores asociados.
