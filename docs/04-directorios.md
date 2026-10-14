# Directorios
En Linux, todo parte de un directorio raíz representado por /. No debe confundirse con /root, que es el directorio personal del usuario administrador.

Esta es una primera referencia de los directorios que conviene conocer:

| Directorio |	Función principal |
|---|---|
| `/`    | Raíz de toda la jerarquía de directorios. |
| `/home`| Directorios personales de los usuarios comunes. |
| `/root`| Directorio personal de root. |
| `/etc` | Archivos de configuración del sistema y de servicios. |
| `/var` | Datos variables: registros, cachés, colas y otros datos de servicios. |
| `/usr` | Gran parte de los programas, bibliotecas y recursos instalados. |
| `/opt` | Software adicional y aplicaciones instaladas fuera de la distribución habitual. |
| `/tmp` | Archivos temporales; su limpieza depende de la configuración del sistema. |
| `/dev` | Archivos de dispositivo que representan interfaces con dispositivos y recursos del sistema. |
| `/proc`| Sistema de archivos virtual que expone información de procesos y del kernel. |
| `/sys` | Información e interfaces del kernel relacionadas con dispositivos y otros objetos del sistema. |
| `/mnt` | Punto de montaje convencional para montajes temporales o administrativos. |
| `/media`| Punto de montaje convencional para medios extraíbles. |
| `/boot`| Archivos necesarios para el arranque, como kernels e imágenes de inicio. |

> Estas funciones son las habituales; los contenidos concretos pueden variar según la distribución y los servicios instalados.

## **4.1 Rutas Relativas/Absolutas**

Antes de estudiar todos los directorios, vamos a dominar cómo movernos entre ellos. Esto también se relaciona con lo que te ocurrió anteriormente al intentar consultar una ruta relativa cuando querías acceder a `/home/lucacoss`.
Una ruta absoluta comienza desde `/`. Una ruta relativa se interpreta desde tu directorio actual.

Por ejemplo, si nos posicionamos en `/home/lucacoss`:

| Comando o ruta |	Significado |
|---|---|
| `/etc` | Ruta absoluta al directorio de configuración. |
| Documents | Directorio Documents dentro de tu ubicación actual, si existe. |
| `..` | Directorio padre. |
| `.`  | Directorio actual. |
| `~`  | Directorio personal del usuario actual en una shell habitual. |

Despues hay comandos interesantes como `realpath` que permite obtener una ruta absoluta para una ruta existente, resolviendo componentes como `.` y `..`.

## **4.2 Jerarquia de directorios**

En Linux, todos los archivos y directorios se organizan a partir de la raíz `/`. No existen letras de unidad como C: o D: al estilo de Windows. Otros discos y particiones se incorporan a la jerarquía mediante puntos de montaje.

## Directorios fundamentales

### `/etc` — Configuración del sistema
Contiene archivos de configuración del sistema operativo y de los servicios instalados. Generalmente son archivos de texto que definen cómo deben comportarse los distintos componentes.

Ejemplos importantes:

| Ruta | Función |
|---|---|
|`/etc/passwd	`| Información básica de las cuentas de usuario. |
|`/etc/shadow	`| Información protegida relacionada con las contraseñas. |
|`/etc/group	`| Información de los grupos locales. |
|`/etc/hostname	`| Nombre del equipo. |
|`/etc/hosts	`| Asociaciones locales entre nombres y direcciones IP. |
|`/etc/fstab	`| Configuración de sistemas de archivos que pueden montarse automáticamente. |
|`/etc/ssh/sshd_config` | Configuración del servidor SSH, si está instalado. |
|`/etc/systemd/`	| Configuración de diversos componentes de systemd. |

Ejemplo práctico: si necesitás revisar la configuración del servidor SSH, buscarías sus archivos en `/etc/ssh/`.

``` bash
ls -ld /etc/ssh/
```

### `/var` — Datos variables

El directorio `/var` almacena información que cambia durante el funcionamiento del sistema. Es especialmente importante en servidores porque ahí suelen crecer los registros, las cachés y los datos de aplicaciones.

|Ruta |	Función |
|---|---|
| `/var/log/` |	Registros del sistema y de las aplicaciones. |
| `/var/cache/` |	Datos de caché que pueden regenerarse. |
| `/var/lib/` |	Estado persistente de aplicaciones y servicios. |
| `/var/spool/` |	Datos pendientes de procesamiento, como colas de trabajos. |
| `/var/tmp/` |	Archivos temporales que, según las convenciones del sistema, pueden conservarse más tiempo que los de /tmp. |
| `/var/log/journal/` |	Ubicación posible de registros persistentes de systemd-journald. |

Ejemplos de administración:
- Si el disco se llena, revisar `/var/log` puede ayudar a detectar registros que crecieron demasiado.
- Si una aplicación pierde su estado al reiniciarse, conviene conocer qué guarda en `/var/lib`.
- Si usás `apt`, sus cachés y datos de paquetes pueden ocupar espacio bajo `/var`.

``` bash
ls -ld /var/log /var/cache /var/lib
```

### `/usr` — Programas, bibliotecas y recursos

`/usr` contiene gran parte de los programas, bibliotecas y recursos instalados en el sistema. 

| Ruta | Función |
|---|---|
| `/usr/bin/  `| Gran parte de los comandos y programas de usuario. |
| `/usr/sbin/ `| Herramientas administrativas; la separación respecto de /usr/bin depende de la distribución. |
| `/usr/lib/`  | Bibliotecas y componentes de software. |
| `/usr/share/`| Datos compartidos independientes de la arquitectura, como documentación. |
| `/usr/local/`| Software instalado localmente por el administrador, fuera de la gestión habitual de paquetes de la distribución. |

Por ejemplo, el comando `ls` suele encontrarse en `/usr/bin/ls`.

``` bash
command -v ls
ls -ld /usr/bin /usr/lib /usr/share /usr/local
```

### `/opt` — Software adicional

`/opt` se utiliza convencionalmente para paquetes de software adicionales. Es una ubicación apropiada para determinadas aplicaciones que mantienen sus propios directorios de instalación.

``` bash
ls -ld /opt
ls -ld /opt/syslab
```

### `/home` y `/root` — Directorios personales

`/home` contiene habitualmente los directorios personales de los usuarios comunes. Cada usuario puede almacenar ahí sus documentos, configuraciones personales y otros archivos. En este servidor:

``` text
/home/lucacoss
/home/labuser
/home/devuser
```

`/root`, en cambio, es el directorio personal de la cuenta de administración root. No es la raíz del sistema: esa es /.

Podés comprobar las rutas existentes con:

``` bash
ls -ld /home /home/lucacoss /root
```

La consulta de `/root` puede mostrar permisos restrictivos, lo cual es normal.

### `/tmp` y `/var/tmp` — Archivos temporales

Ambos directorios permiten guardar información temporal, pero existe una diferencia convencional:

- `/tmp` se utiliza para archivos temporales que pueden eliminarse durante el arranque o por tareas de limpieza.
- `/var/tmp` se destina a archivos temporales que, en general, deben poder persistir durante más tiempo.

La política exacta depende de la distribución y su configuración. No asumas que todo lo que está en /tmp desaparece inmediatamente al reiniciar.
Podés inspeccionar sus permisos con:

``` bash
ls -ld /tmp /var/tmp
```

Es habitual encontrar permisos como `drwxrwxrwt` en estos directorios. La `t` final indica el sticky bit, que restringe quién puede eliminar o renombrar entradas en un directorio compartido.
Ese es uno de los usos prácticos de los permisos especiales que vimos anteriormente.

### `/dev` — Dispositivos

Linux representa muchos dispositivos mediante archivos especiales bajo `/dev`. No son archivos ordinarios que contengan datos de la manera habitual.
Ejemplos comunes:

- /dev/sda: un dispositivo de bloques, como un disco.
- /dev/sda2: una partición de ese disco.
- /dev/null: descarta los datos escritos en él.
- /dev/zero: proporciona bytes nulos al leerlo.
- /dev/tty: representa el terminal asociado al proceso, cuando corresponde.

En este servidor, vemos `/dev/sda2` en la salida de findmnt `/`. Esa partición contiene el sistema de archivos raíz.
Para comprobar algunos dispositivos:

```bash
ls -l /dev/null /dev/zero
ls -l /dev/sda /dev/sda2
```

Los últimos elementos pueden variar según la configuración y los dispositivos que exponga la máquina virtual.

### `/boot` — Archivos de arranque

Contiene archivos necesarios para iniciar el sistema, como kernels e imágenes de inicio. Para inspeccionarlo:

``` bash
ls -lh /boot
```

Su contenido depende de la distribución, el kernel instalado y la configuración del arranque. No conviene borrar manualmente archivos de este directorio: una limpieza incorrecta puede dejar el sistema sin poder arrancar.

### `/mnt` y `/media` — Puntos de montaje

Son ubicaciones convencionales para montar sistemas de archivos:

- /mnt: suele utilizarse para montajes temporales o administrativos.
- /media: suele utilizarse para medios extraíbles.

Un punto de montaje es un directorio donde se hace accesible el contenido de otro sistema de archivos. El contenido que había debajo del directorio antes del montaje queda oculto mientras el montaje está activo. Para consultar los montajes actuales:

``` bash
findmnt
```
