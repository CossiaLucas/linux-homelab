# **Sistema de archivos**

## Practica completa sobre los directorios

1. Identificar el hostname 

``` bash
cat /etc/hostname
```

2. Examinar el espacio de los logs

``` bash
sudo du -sh /var/log
```

3. Consultar capacidad, espacio usado, espacio disponible, porcentaje de uso y tipo de sistema de archivos.

``` bash
df -hT
```

4. Consultar los permisos de `/tmp`

```bash
ls -ld /tmp
```

5. Ver las interfaces de red

``` bash
ls /sys/class/net
```

6. Identificar dispositivos, particiones y sistemas de archivos.

``` bash
lsblk -f
```

7. Identificar qué sistema de archivos contiene el directorio personal

``` bash
findmnt -T /home/lucacoss
```

[Respuesta de los comandos](../../../screenshots/system/img06.png)

Aca podemos evaluar varias cosas:

1. El contenido de `/etc/hostname` coincide con el nombre que venimos utilizando. Podemos registrar esta verificación como completada.

2. Los archivos y subdirectorios de logs ocupan aproximadamente `37 MB`.
Es un tamaño razonable para un servidor recién instalado y en preparación, aunque el valor por sí solo no permite determinar si los registros son suficientes o si hay algún problema.

> Este resultado corresponde al espacio ocupado por `/var/log`, no al total del almacenamiento del sistema.

3. Capacidad y uso del sistema de archivos

La salida de `df -hT` muestra:

| Punto de montaje | Tipo | Tamaño | Usado | Disponible | Uso |
|---|---|---|---|---|---|
| `/`	    | `ext4`  |	25 GB  | 5,7 GB	| 18 GB  | 	25 % |
| `/run`	| `tmpfs` |	329 MB | 1,1 MB	| 328 MB |	1 %  |
| `/dev/shm`| `tmpfs` |	822 MB | 0	    | 822 MB |	0 %  |
| `/tmp`    | `tmpfs` |	165 MB | 8 KB	| 165 MB |	1 %  |

La observación principal es que la raíz `/` está montada sobre `/dev/sda2`, usa `ext4` y tiene aproximadamente un 25 % de ocupación. Por lo tanto, todavía dispone de bastante espacio para las siguientes prácticas.
También aparece `tmpfs`, un sistema de archivos temporal respaldado principalmente por memoria. No es una partición convencional del disco. 

4. Permisos de `/tmp`

``` text
drwxrwxrwt 13 root root 260 ... /tmp
```

Este resultado es especialmente interesante.

- `root:root`: el propietario y el grupo son root.
- `rwxrwxrwt`: propietario, grupo y otros tienen permisos de lectura, escritura y acceso al directorio.
- `t`: indica el sticky bit.

El sticky bit es importante en directorios compartidos como `/tmp`: aunque distintos usuarios puedan crear archivos allí, normalmente un usuario no puede borrar o renombrar los archivos de otro usuario solo por tener permisos de escritura en el directorio. El propietario del archivo, el propietario del directorio y root son excepciones habituales.

5. Interfaces de red

``` text
enp0s3
lo
```

Encontramos dos interfaces:
- `lo`: la interfaz de loopback, utilizada para la comunicación local del propio sistema.
- `enp0s3`: la interfaz de red virtual que venimos asociando a la VM de VirtualBox.
Con este comando identificamos sus nombres, pero todavía no verificamos sus direcciones IP ni la ruta predeterminada. Eso lo haremos en la etapa 4.

6. Dispositivos y particiones

Resumidamente:

``` text
sda
├─sda1
└─sda2 ext4 /
```

La partición `/dev/sda2` contiene el sistema de archivos `ext4` montado en `/`. En el resultado aparece también `sda1`, pero no tiene un sistema de archivos visible en las columnas mostradas.

7. Sistema de archivos del directorio personal

``` text
TARGET SOURCE    FSTYPE OPTIONS
/      /dev/sda2 ext4   rw,relatime
```

`findmnt -T /home/lucacoss` nos confirma que el directorio personal está contenido en el sistema de archivos raíz. No tiene una partición independiente para `/home`.
Esto es coherente con una instalación sencilla: una única partición principal contiene tanto el sistema operativo como los directorios personales.

8. Vamos a realizar un ultimo comando

``` bash
df -i /
``` 

Con la respuesta:

``` text
Filesystem  Inodes  Iused   Ifree   Iuse%   Monted on
/dev/sda2   1638400 151455  1486945 10%     /
```

## Nuevos grupos con nuevos directorios

Ya tenemos el grupo syslab, vamos a poner un nuevo grupo llamado soporte

``` bash
sudo groupadd soporte
sudo usermod -aG soporte labuser
``` 

Despues vamos a crear nuevos directorios que van a pertenecer a distintos propietarios

``` bash
sudo install -d -o root -g root -m 755 /stv/areas
sudo install -d -o root -g syslab -m 2770 /stv/areas/desarrollo
sudo install -d -o root -g soporte -m 2770 /stv/areas/soporte
```

> Utilizamos `install -d` Porque permite crear directorios y establecer su propietario, grupo y permisos en una misma operación.

> Me confundi con el directorio, se tenia que escribir /srv y le erre a la tecla :)

Finalmente creamos unos archivos

``` bash
sudo -u devuser touch /stv/areas/desarrollo/prueba-devuser.txt
sudo -u labuser touch /stv/areas/soporte/prueba-labuser.txt
```

Y verificamos los archivos creados

``` bash
ls -l /srv/areas/desarrollo
ls -l /srv/areas/soporte
```