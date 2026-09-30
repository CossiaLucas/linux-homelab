# Administración del sistema

Documentación relacionada con la administración del sistema operativo de `srv-linux01`.

---

## Objetivos

Practicar:

- Usuarios.
- Grupos.
- Permisos.
- Procesos.
- Servicios.
- systemd.
- Logs.
- Paquetes.
- Información del sistema.
- Administración básica.

---

# Administración del sistema

## 1. Actualización del sistema

Primero vamos a realizar una actualizacion de paquetes y librerias para que todo funcione correctamente. Esto lo podemos realizar con.

Actualización del sistema

``` bash
sudo apt update
```

![Respuesta del update](../../../screenshots/system/img01.png)

Y despues:

``` bash
sudo apt upgrade
```

![Respuesta del upgrade](../../../screenshots/system/img02.png)

## 2. Usuarios y grupos

Antes de comenzar a crear y modificar usuarios, se identificó la cuenta utilizada actualmente en `srv-linux01`.

``` bash
whoami
```

``` bash
id
```

Con las respuestas recibidas respectivamente:

``` text
lucacoss
```

``` text
uid=1000(lucacoss) gid=1000(lucacoss) groups=1000(lucacoss),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),100(users),101(lxd)
```

el comando id es el que nos interesa especialmente porque muestra:

* `UID` → Identificador numérico del usuario.

* `GID` → Grupo principal.

Linux identifica internamente al usuario mediante este número, no solamente mediante lucacoss.

Y el GID corresponda al grupo lucacoss.

* grupos secundarios.

### 2.1 Grupos 

Tambien podemos ver los grupos a los que pertenece nuestro usuario. Mediante el comando.

``` bash
groups
```

Con sus respectivas respuestas: 

![Respuestas de comandos](../../../screenshots/system/img03.png)

Vamos a ver los distintos grupos porque es bastante interesante analizarlos y entenderlos para despues ponernos a crear usuarios.

* `sudo`

El grupo `sudo` permite que sus miembros puedan utilizar `sudo`, sujeto a las reglas configuradas en `/etc/sudoers` y `/etc/sudoers.d/`.

``` bash
sudo whoami
```
respondió con: `root`

* `adm`

El grupo `adm` está relacionado con el acceso a determinados archivos de logs del sistema.

Esto es muy interesante porque despues vamos a estar trabajando con:

``` bash
journalctl
```

* `cdrom`

Permite acceder a dispositivos opticos cuando están configurados de esa manera. No es relevante para nuestro servidor, pero Ubuntu lo incluye en determinados perfiles de usuario.

* `dip`

Está relacionado con determinadas interfaces/configuraciones de red mediante herramientas tradicionales de Linux.

* `plugdev`

Esta relacionado con el acceso a determinados dispositivos extraibles.

* `users`

Grupo general de usuarios.

* `lxd`

### 2.2 Informacion adicional

Posteriormente, para consultar la información de la cuenta se utilizó:

``` bash
getent passwd lucacoss
```

El resultado fue:

```text
lucacoss:x:1000:1000:Lucas Cossia:/home/lucacoss:/bin/bash
```

La entrada puede interpretarse de la siguiente manera:

usuario : contraseña : UID : GID : comentario : home : shell

Este es particularmente interesante para nosotros porque Ubuntu incluye al usuario en el grupo lxd, que está relacionado con LXD, la tecnologia de gestión de contenedores/máquinas virtuales de Canonical.

Para nosotros:

| Campo             | Valor            |
| ----------------- | ---------------- |
| Usuario           | `lucacoss`       |
| UID               | `1000`           |
| GID               | `1000`           |
| Nombre/comentario | `Lucas Cossia`   |
| Home              | `/home/lucacoss` |
| Shell             | `/bin/bash`      |

### 2.3 `/etc/passwd`

El archivo `/etc/passwd` contiene información básica sobre las cuentas del sistema.

Sus permisos se verificaron mediante:

``` bash
ls -l /etc/passwd
```

Resultado:

``` text
-rw-r--r-- 1 root root 1750 Sep 30 00:28 /etc/passwd
```

El archivo pertenece al usuario `root` y al grupo `root`.

Los permisos son:

``` text
rw-r--r--
```

Por lo tanto:

`root` puede leer y modificar el archivo.
El grupo `root` puede leerlo.
**Otros usuarios** pueden leerlo.
Los **usuarios normales** no pueden modificarlo directamente.
2.4 `/etc/shadow`

La información relacionada con la autenticación se almacena en `/etc/shadow`.

Sus permisos se verificaron mediante:

``` bash
ls -l /etc/shadow
```

Resultado:

``` text
-rw-r----- 1 root shadow 846 Sep 30 00:28 /etc/shadow
```

`root` puede leer y modificar el archivo.
Los miembros del grupo `shadow` pueden leerlo.
Los demás usuarios no tienen permisos sobre el archivo.

Esta diferencia de permisos es importante porque `/etc/shadow` contiene
información sensible relacionada con la autenticación de los usuarios.

### 2.4 Directorio personal

La cuenta lucacoss tiene como directorio personal:

`/home/lucacoss`

Se verificó mediante:

``` bash
ls -ld /home/lucacoss
```

Resultado:

``` text
drwx------ 4 lucacoss lucacoss 4096 Sep 30 02:17 /home/lucacoss
```

El directorio pertenece al usuario lucacoss y al grupo `lucacoss`.

Los permisos drwx------ indican que el propietario tiene permisos sobre el
directorio, mientras que el grupo y otros usuarios no tienen permisos.

Durante la verificación también se ejecutó accidentalmente:

``` bash
ls -ld lucacoss
```

que produjo:

``` text
ls: cannot access 'lucacoss': No such file or directory
```

Esto ocurrió porque lucacoss fue interpretado como una ruta relativa desde
el directorio actual.

La ruta correcta del directorio personal es:

``` text
/home/lucacoss
```

Este caso permitió comprobar en la práctica la diferencia entre una ruta
relativa y una ruta absoluta.



## 3. Privilegios y sudo

## 4. Sistema de archivos

## 5. Procesos

## 6. Servicios y systemd

## 7. Logs y journalctl

## 8. Gestión de paquetes

## 9. Información y diagnóstico del sistema

## 10. Tareas de administración

## 11. Problemas encontrados y soluciones