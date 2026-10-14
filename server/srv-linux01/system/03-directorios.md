# **Sistema de archivos**

Vamos a ejercutar unos comandos sencillos para saber como orientarnos mejor

``` bash
pwd
ls -la /
ls -la /home
findmnt /
df -h /
```

* `pwd` indica en qué directorio estás ubicado.

* `ls -la /` muestra el contenido de la raíz, incluidos los elementos ocultos.

* `findmnt /` permite identificar qué sistema de archivos está montado en `/`.

``` text
TARGET SOURCE     FSTYPE OPTIONS
/      /dev/sda2  ext4   rw,relatime
```

> `/dev/sda2` es una partición del disco virtual que utiliza tu máquina virtual. No es un directorio, sino un dispositivo de bloques que contiene el sistema de archivos.

* `df -h /` muestra el espacio total, utilizado y disponible del sistema de archivos que contiene la raíz.

``` text
Filesystem   Size  Used  Avail  Use%  Mounted on
/dev/sda2     25G   5.7G   18G    25%  /
```

![Repuesta de los comandos](../../../screenshots/system/img05.png)


## Practica sobre directorios fundamentales

1. Identificar el hostname 

``` bash
cat /etc/hostnmae
```

2. Examinar el espacio de los logs

``` bash
sudo du -sh /var/logs
```

3. Localizar el comando `ls`

``` bash
command -v ls
```

4. Consultar los permisos de `/tmp`

```bash
ls -ld /tmp
```

5. Ver las interfaces de red

``` bash
ls /sys/class/net
```

6. Examinar los puntos de montaje

```bash
finmnt
```