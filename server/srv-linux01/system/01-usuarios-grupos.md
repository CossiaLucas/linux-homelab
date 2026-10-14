# **2. Usuarios y grupos**

Antes de comenzar a crear y modificar usuarios, se identificó la cuenta utilizada actualmente en `srv-linux01`.

``` bash
whoami
    lucacoss
```

``` bash
id
    uid=1000(lucacoss) gid=1000(lucacoss) groups=1000(lucacoss),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),100(users),101(lxd)
```

el comando id es el que nos interesa especialmente porque muestra:

* `UID` → Identificador numérico del usuario.

* `GID` → Grupo principal.

Linux identifica internamente al usuario mediante este número, no solamente mediante lucacoss.

Y el GID corresponda al grupo lucacoss.

## **2.1 Grupos **

Tambien podemos ver los grupos a los que pertenece nuestro usuario. Mediante el comando.

``` bash
groups
```

Con su respectiva respuesta: 

![Respuestas de comandos](../../../screenshots/system/img03.png)

## **2.2 Informacion adicional**

Posteriormente, para consultar la información de la cuenta se utilizó:

``` bash
getent passwd lucacoss
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

## **2.3 `/etc/passwd`**

El archivo `/etc/passwd` contiene información básica sobre las cuentas del sistema.

Sus permisos se verificaron mediante:

``` bash
ls -l /etc/passwd
    -rw-r--r-- 1 root root 1750 Sep 30 00:28 /etc/passwd
```

El archivo pertenece al usuario `root` y al grupo `root`.

Los permisos son:

``` text
rw-r--r--
```

## **2.4 `/etc/shadow`**

La información relacionada con la autenticación se almacena en `/etc/shadow`.

Sus permisos se verificaron mediante:

``` bash
ls -l /etc/shadow
    -rw-r----- 1 root shadow 846 Sep 30 00:28 /etc/shadow
```

`root` puede leer y modificar el archivo.
Los miembros del grupo `shadow` pueden leerlo.
Los demás usuarios no tienen permisos sobre el archivo.

Esta diferencia de permisos es importante porque `/etc/shadow` contiene
información sensible relacionada con la autenticación de los usuarios.

## **2.5 Creacion de un usuario**

Vamos a crear un usuario llamado: `labuser`.

Para eso ejecutamos el comando.

``` bash
sudo useradd -m -s /bin/bash labuser
```

El comando `useradd` permite crear la cuenta del usuario. En este caso utilizamos algunas opciones para configurar desde el principio el directorio personal y el shell de inicio de sesión. En particular, queremos que nuestro usuario de laboratorio tambien tenga su propio `/home` y un shell definido.

Por eso utilizamos los parametros.

* `-m` → crea el directorio /home/labuser.
* `-s /bin/bash` → establece Bash como shell de inicio de sesión.
* `labuser` → nombre de la cuenta.

Otra cosa que ocurre es que una cuenta recién creada no tiene necesariamente una clave utilizable para iniciar sesión. Por ende vamos a establecerla:

``` bash
sudo passwd labuser
```

Con esto, el sistema va a pedir dos veces la clave, para establecerla correctamente. 

## **2.6 Validacion de la creacion del usuario**

Ahora vamos a proceder a validar que todo se haya creado correctamente

``` bash
id labuser
    uid=1001(labuser) gid=1001(labuser) groups=1001(labuser)
```

> El UID puede variar por el sistema. Puede ocurrir que no sea 1001.

Y tambien:

``` bash
getent passwd labuser
    labuser:x:1001:1001::/home/labuser:/bin/bash
```

Despues verificamos el directorio `/home`:

``` bash 
ls -ld /home/labuser
    drwxr-x--- 2 labuser labuser 2096 Sep 30 22:46 /home/labuser
```

Y finalmente su shell asociado:

``` bash
getent passwd labuser
    /bin/bash
```

Por ende todo se realizo correctamente. 

## **2.7 Creacion de grupos secundarios**

Creemos un grupo específico para nuestro laboratorio:

``` bash
sudo groupadd syslab
```

Y ahora agregamos `labuser` al grupo:

``` bash
sudo usermod -aG syslab labuser
```

* `-a` -> append: agrega el grupo sin eliminar los grupos secundarios existentes.

* `-G` -> Indica los grupos secundarios.

> No utilizamos solo `-G`. Pues, establece la lista de grupos secundarios indicada. Si el usuario ya perteneciera a otros grupos secundarios, podríamos reemplazar esa lista accidentalmente.

Ahora verificamos los cambios

``` bash
id labuser
    uid=1001(labuser) gid=1001(labuser) groups=1001(labuser),1002(syslab)
```

``` bash
groups labuser
    labuser : labuser syslab
```

## **2.8 Modificacion del usuario**

Aca podemos realizar modificaciones como:

``` bash
sudo usermod -c "Usuario de laboratorio" labuser
```

* El parametro `-c` modifica el campo de comentario o información descriptiva de la cuenta.

Y despues lo vereficamos

``` bash
getent passwd labuser
    labuser:x:1001:1001:Usuario de laboratorio:/home/labuser:/bin/bash
```

Hay mas parametros que podemos utilizar como:

* `-s` → Establece o modifica el shell de inicio de sesión de la cuenta.

Tambien podemos modificar su clave con `sudo passwd labuser` o bloquear la cuenta con:

``` bash
sudo passwd -l labuser
```

Consultar su estado:

``` bash
sudo passwd -S labuser
```

O desbloquear su clave:

``` bash
sudo passwd -u labuser
```

De esta manera podemos deshabilitar temporalmente la autenticación mediante la clave de una cuenta sin eliminarla, lo que puede utilizarse como una forma de baja lógica.