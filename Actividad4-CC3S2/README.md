# Actividad 4: Introducción a Herramientas CLI en Entornos Unix-like

En este documento README.md detallaré la ejecución delos ejercicios correspondientes a esta actividad.

- **Alumno:** Walter Poma
- **Fecha:** 16 de Septiembre de 2025

Se presenta el archivo sesion_redactada.txt como medida de seguridad para tachar cualquier información sensible como se indica en l actividad.

Para poder subir la actividad a mi repositorio estoy dando permisos de lectura del archivo secreto.txt.

---

## Sección 1: Manejo sólido de CLI

En esta sección se practicaron los comandos básicos de navegación, manipulación de archivos y el uso de tuberías y redirecciones.

Se navegó al directorio /etc, se listaron todos sus contenidos (incluyendo archivos ocultos) y la salida fue redirigida a un archivo en el directorio home.

- **Comando Ejecutado:**
  ```bash
  cd /etc && ls -a > ~/etc_lista.txt
Se verificó la correcta creación del archivo mediante el comando head ~/etc_lista.txt.
- **Evidencia:**
  ```bash
    .
    ..
    .pwd.lock
    .resolv.conf.systemd-resolved.bak
    .updated
    PackageKit
    X11
    adduser.conf
    alternatives
    apache2
Luego se utilizó patrones para encontrar archivos con extensión .txt o .doc en el directorio /tmp
- **Comando Ejecutado:**
    ```bash
    find /tmp -maxdepth 1 -type f \( -name '*.txt' -o -name '*.doc' \) | wc -l
El comando arrojó un número como salida.
- **Evidencia:**
    ```bash
    4
---

## Sección 2: Administración Básica

Esta sección cubrió la gestión de usuarios, grupos y permisos de archivos.

Se crearon un usuario (devsec) y un grupo (ops), y se asignaron permisos específicos a un archivo para restringir su acceso
- **Comando Ejecutado:**
    ```bash
    sudo adduser devsec
    sudo addgroup ops
    sudo usermod -aG ops devsec
    touch secreto.txt
    sudo chown devsec:ops secreto.txt
    sudo chmod 640 secreto.txt
LA correcta asignación de propietario y permiuso se verifico con 'ls -l secreto.txt' y la membresía del grupo con id devsec. 
- **Evidencia:**
    ```bash
    ~/DesarrolloSoftware/Curso-CC3S2/Actividad4-CC3S2$ ls -l secreto.txt
    -rw-r----- 1 devsec ops 0 Sep 16 14:02 secreto.txt
    ~/DesarrolloSoftware/Curso-CC3S2/Actividad4-CC3S2$ id devsec
    uid=1001(devsec) gid=1001(devsec) groups=1001(devsec),100(users),1002(ops)
Se listaron los procesos del sistema para identificar el ID de Proceso (PID) del shell bash actual.
- **Comando Ejecutado:**
    ```bash
    ps aux | grep bash
Se identificó el PID del proceso bash que es la segunda columna de la salida, por ejemplo un PID identificado fue 386.
- **Evidencia:**
    ```bash
    walter       386  0.0  0.0   6072  5128 pts/1    S+   01:39   0:00 -bash
    walter     28264  0.0  0.0   7288  6532 pts/4    Ss+  04:16   0:00 -bash
    walter     59713  0.0  0.0   6204  5444 pts/5    Ss+  07:06   0:00 -bash
    walter     93697  0.0  0.0   6204  5416 pts/0    Ss+  11:36   0:00 -bash
    walter    105292  0.0  0.0   7428  6488 pts/7    Ss+  12:40   0:00 /bin/bash --init-file /home/walter/.vscode-server/bin/f220831ea2d946c0dcb0f3eaa480eb435a2c1260/out/vs/workbench/contrib/terminal/common/scripts/shellIntegration-bash.sh
    walter    116868  0.0  0.0   6204  5500 pts/8    Ss   13:32   0:00 -bash
    walter    118634  0.0  0.0   6212  5460 pts/9    Ss   13:42   0:00 bash -i
    walter    122576  0.0  0.0   4088  1936 pts/9    S+   14:04   0:00 grep --color=auto bash
Se usó systemcl para verificar el estado de un servicio y journal para ver los registros mas recientes.
- **Comando Ejecutado:**
    ```bash
    systemctl status systemd-logind
    journalctl -u systemd-logind -n 10
El primer comando mostró el estado active y el segundo listó las últimas 10 entradas de log.
- **Evidencia:**
    ```bash
    ● systemd-logind.service - User Login Management
        Loaded: loaded (/usr/lib/systemd/system/systemd-logind.service; static)
        Drop-In: /usr/lib/systemd/system/systemd-logind.service.d
                └─dbus.conf
        Active: active (running) since Sun 2025-09-14 20:59:09 -05; 1 day 17h ago
        Docs: man:sd-login(3)
                man:systemd-logind.service(8)
                man:logind.conf(5)
                man:org.freedesktop.login1(5)
    Main PID: 167 (systemd-logind)
        Status: "Processing requests..."
        Tasks: 1 (limit: 19094)
    FD Store: 0 (limit: 512)
        Memory: 1.6M ()
        CGroup: /system.slice/systemd-logind.service
                └─167 /usr/lib/systemd/systemd-logind

    Sep 14 20:59:09 Galium-PC systemd[1]: Starting systemd-logind.service - User Login Management...
    Sep 14 20:59:09 Galium-PC systemd-logind[167]: New seat seat0.
    Sep 14 20:59:09 Galium-PC systemd[1]: Started systemd-logind.service - User Login Management.
    Sep 14 20:59:11 Galium-PC systemd-logind[167]: New session 1 of user walter.
- **Segundo comando:**
    ```bash
    Sep 13 09:08:49 Galium-PC systemd[1]: Started systemd-logind.service - User Login Management.
    Sep 13 09:08:51 Galium-PC systemd-logind[186]: New session 1 of user walter.
    -- Boot 772433a1760e4c88bcb3d342286aaacc --
    Sep 14 19:01:34 Galium-PC systemd[1]: Starting systemd-logind.service - User Login Management...
    Sep 14 19:01:34 Galium-PC systemd-logind[186]: New seat seat0.
    Sep 14 19:01:34 Galium-PC systemd[1]: Started systemd-logind.service - User Login Management.
    Sep 14 19:01:36 Galium-PC systemd-logind[186]: New session 1 of user walter.
    -- Boot 0700950328f147a39a77144e1bdd26c6 --
    Sep 14 20:59:09 Galium-PC systemd[1]: Starting systemd-logind.service - User Login Management...
    Sep 14 20:59:09 Galium-PC systemd-logind[167]: New seat seat0.
    Sep 14 20:59:09 Galium-PC systemd[1]: Started systemd-logind.service - User Login Management.
    Sep 14 20:59:11 Galium-PC systemd-logind[167]: New session 1 of user walter.

## Sección 3: Utilidades de Texto de Unix

Se usó grep para encontrar y mostrar todas las líneas que contienen la palabra "root" en /etc/passwd.

- **Comando Ejecutado:**
    ```bash
    grep root /etc/passwd
- **Resulatdo:**
    ```bash
    root:x:0:0:root:/root:/bin/bash

Se reemplazó un texto dentro de un archivo y se guardó el resultado en un nuevo archivo usando sed.

- **Comando Ejecutado:**
    ```bash
    printf "linea1: dato1\nlinea2: dato2\n" > datos.txt
    sed 's/dato1/secreto/' datos.txt > nuevo.txt
La evidencia se ve en el archivo nuevo.txt.
- **Evidencia:**
    ```bash
    ~/DesarrolloSoftware/Curso-CC3S2/Actividad4-CC3S2$ cat nuevo.txt
    Linea1: secreto
    Linea2:dato
Se construyó un pipeline para extraer los nombres de usuario de /etc/passwd.

- **Comando Ejecutado:**
    ```bash
    awk -F: '{print $1}' /etc/passwd | sort | uniq

- **Evidencia:**
    ```bash
    _apt
    backup
    bin
    daemon
    devsec
    dhcpcd
    games
    irc
    landscape
    list
    lp
    mail
    man
    ...
Se convirtió un texto a Mayúscula con tr y tee
- **Comando Ejecutado:**
    ```bash
    printf "hola\n" | tr 'a-z' 'A-Z' | tee mayus.txt
Se mostró HOLA y se creó el archivo mayus.txt
- **Evidencia:**
    ```bash
    HOLA