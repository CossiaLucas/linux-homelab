# Grupos secundarios

Durante la inspeccion de grupos del usuario `lucacoss` recibimos esta respuesta ante el comando `groups`.

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

---

# Diferencia de permisos

Algo que podemos mostrar es la diferencia que hay entre los usuarios `lucacoss` y `labuser`. Si nosotros entramos temporalmente a labuser con 

``` bash
su - labuser
```

Y ejecutamos:

``` bash
sudo whoami
    sudo: I´m sorry labuser. I´m afraid I can´t do that
```

Despues salimos con `exit`.

Como `labuser` no esta en el grupo `sudo` no tiente aquellos permisos.

---

# `Umask`

Durante la interaccion con un archivo de prueba en el directorio `/opt/syslab` detectamos que, a pesar, de que el grupo propietario del directorio `syslab` sea, justamente, `syslab` el archivo quedo con el grupo propietario `labuser`.

Esto ocurrio porque un directorio no hereda automáticamente su **grupo propietario** a los archivos nuevos que se crean dentro de él.
Por defecto, el nuevo archivo utiliza el grupo principal del usuario que lo crea.

Si ejecutamos:

``` bash
umask
    0002
```

La `umask` determina qué permisos se eliminan de los permisos iniciales al crear archivos y directorios. En nuestro caso, el valor `0002` contribuyó a que el archivo se creara con permisos `664` (`rw-rw-r--`).
El grupo propietario del archivo es un aspecto diferente y, en este caso, corresponde al grupo principal de `labuser`.

Y esto nos lleva a una herramienta muy interesante de Linux: `setgid`. En un directorio, setgid hace que los nuevos archivos y subdirectorios creados dentro de él hereden el grupo propietario del directorio.