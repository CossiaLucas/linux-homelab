# **3. Permisos, propietarios y grupos**

En Linux, cada archivo y directorio tiene principalmente:
* un propietario;
* un grupo propietario;
* permisos para el propietario;
* permisos para el grupo;
* permisos para otros usuarios.´

Vamos a trabajar con labuser y el grupo syslab que ya creamos.

Primero vamos a crear un directorio destinado a nuestras pruebas:

``` bash
sudo mkdir /opt/syslab
```

El motivo es que `/opt` es un lugar apropiado para software o recursos adicionales del sistema. En nuestro caso lo utilizaremos simplemente como un recurso de laboratorio. Ahora verificamos cómo quedó:

``` bash
ls -ld /opt/syslab
    drwxr-xr-x 2 root root 4096 Oct 1 00:09
```

Aca podemos hacer un analisis basico de permisos:

* Primero. Un analisis completo de la sintaxis

* `d`           → directorio
* `rwx`         → permisos del propietario
* `r-x`         → permisos del grupo
* `r-x`         → permisos de otros
* `2`           → cantidad de enlaces
* `root`        → propietario
* `root`        → grupo propietario
* `4096`        → tamaño
* `Oct 1 00:09` → fecha de modificación
* `/opt/syslab` → ruta

* Segundo. Tenemos 3 grupos de 3 tipos de permisos

    rwx - Propietario

    r-x - Grupo

    r-x - Otros

| Permiso | Significado |
|---|---|
| `r` | read — lectura |
| `w` | write — escritura |
| `x` | execute — ejecución |

## **3.1 Cambiar propietarios**

El comando `chown` significa **Change owner**

``` bash
sudo chown labuser /opt/syslab
```

Y lo verificamos 

``` bash
ls -ld /opt/syslab
    drwxr-xr-x 2 labuser root 4096 Oct 1 00:09
```

Y despues esta el comando `chgrp` que significa **Change group**

``` bash
sudo chgrp syslab /opt/syslab
```

Y lo verificamos 

``` bash
ls -ld /opt/syslab  
    drwxr-xr-x 2 labuser syslab 4096 Oct 1 00:09
```

También podríamos haber realizado ambas operaciones mediante `chown`:

``` bash
sudo chown labuser:syslab /opt/syslab
```

## **3.2 Modificacion de permisos**

En esta seccion vamos a usar `chmod`

Primero vamos a explicar otras cosas de los permisos.

* `r = 4`
* `w = 2`
* `x = 1`

| Usuario | Permisos |
|---|---|
| `labuser` | `rwx` |
| grupo `syslab` | `r-x` |
| otros | `---` |

Entonces si realizamos una practica como:

1. Crear el directorio

2. Verificarlo

3. Ver información detallada

4. Cambiar propietario

5. Cambiar grupo

6. Verificar

7. Aplicar permisos

8. Verificamos nuevamente

![Practica de permisos](../../../screenshots/system/img04.png)

## **3.3 Interaccion entre usuarios, grupos y permisos**

Vamos a empezar con unos comandos

``` bash
su - labuser
whoami
    labuser
cd /opt/syslab
pwd
    /opt/syslab
touch prueba.txt
ls -l
    total 0
    -rw-rw-r-- 1 labuser labuser 0 Oct 1 00:50 prueba.txt
```

Aca detectamos una situacion interesante. Aunque el directorio `/opt/syslab` tiene como grupo propietario a `syslab`, el archivo nuevo quedó con el grupo `labuser`.

Primero vamos a salir con `exit`. Despues comprobamos...

``` bash
ls -ld /opt/syslab
    drwxr-x--- 2 labuser syslab 4096 Oct 1 00:50 /opt/syslab
ls -l /opt/syslab
    total0
    -rw-rw-r-- 1 labuser labuser 0 Oct 1 00:50 preuba.txt
```

Esta situacion se puede solucionar con `setgid`.

Actualmente tenemos:
| Ubicacion | dueño:grupo |
|---|---|
| `Directorio` | `labuser:syslab` |
| `Archivo` | `labuser:labuser` |

Pero, por ejemplo, si `syslab` representa un **equipo de trabajo**. Queremos que los archivos creados dentro de `/opt/syslab` pertenezcan automáticamente al grupo: `syslab`

Para eso podemos utilizar `setgid` en el directorio.

``` bash
sudo chmod g+s /opt/syslab
```

> El g+s significa que estamos activando el bit setgid para el grupo del directorio.

Y lo comprobamos:

``` bash
ls -ld /opt/syslab
    drwxr-s--- 2 labuser syslab 4096 Oct 1 00:50 /opt/syslab
```

Antes en los permisos de grupo teniamos `r-x`. Pero ahora tenemos `r-s`. Esa `s` significa que `setgid` esta *activo*.

Ahora vamos a crear otro archivo:

``` bash
su - labuser
whoami
    labuser
touch /opt/syslab/prueba-setgid.txt
ls -l /opt/syslab
    total 0
    -rw-rw-r-- labuser syslab 0 Oct 1 01:17 prueba-setgit.txt
    -rw-rw-r-- labuser labuser 0 Oct 1 00:50 prueba.txt
```

Hasta ahora `labuser` es el propietario del directorio: `labuser:syslab`

y tiene: `rwx`

Mientras que el grupo syslab tiene: `r-x`

El problema es que todavía no tenemos otro usuario utilizando ese grupo. Así que vamos a crear un segundo usuario de laboratorio `:)`.

## **3.4 Permisos**

### **3.4.1 Habilitar escritura para el grupo**

Hasta este punto, el directorio `/opt/syslab` tenía el bit `setgid` activado, pero el grupo `syslab` no disponía de permiso de escritura sobre el directorio.

Por eso, aunque `devuser` (Otro usuario creado para la prueba, con la caracteristica de que pertenece a `syslab`) pertenecía a `syslab`, no podía crear nuevos archivos dentro de él.

Para permitir que los integrantes del grupo creen archivos, modificamos los permisos del directorio:

```bash
sudo chmod g+w /opt/syslab
```

El parámetro `g+w` agrega el permiso de escritura (`w`) al grupo propietario, sin modificar los demás permisos.

Al intentar listar el contenido desde `lucacoss`, recibimos un error de permisos. Esto ocurre porque el directorio no concede acceso a usuarios externos a su propietario y grupo. Para consultar su contenido como administrador, utilizamos:

```bash
sudo ls -l /opt/syslab
    -rw-r--r-- 1 devuser syslab 0 Oct 9 13:20 archivo-devuser.txt
```

Con esto comprobamos que `devuser` pudo crear el archivo `archivo-devuser.txt`, que quedó asociado al grupo `syslab`.

### **3.4.2 Verificación de los permisos del archivo**

Al revisar el archivo mediante `ls -l`, obtuvimos:

```text
-rw-r--r-- 1 devuser syslab 0 Oct 9 13:20 archivo-devuser.txt
```

Podemos interpretar sus permisos de la siguiente manera:

| Categoría | Permisos | Significado |
|---|---|---|
| Propietario (`devuser`) | `rw-` | Puede leer y modificar el archivo |
| Grupo (`syslab`) | `r--` | Puede leer, pero no modificar |
| Otros | `r--` | Pueden leer, pero no modificar |

Aunque el archivo pertenece al grupo `syslab`, sus integrantes no tienen permiso de escritura. Esto explica por qué `labuser` no podía modificarlo.

**La pertenencia a un grupo no concede permisos automáticamente:** el recurso debe otorgar los permisos necesarios a ese grupo.

### **3.4.3 Modificar los permisos del archivo**

Para permitir que los integrantes de `syslab` modifiquen el archivo, ejecutamos como `lucacoss`:

```bash
sudo chmod g+w /opt/syslab/archivo-devuser.txt
```

Este comando agrega el permiso de escritura al grupo propietario del archivo.

De esta manera, los permisos pasan de:

```text
rw-r--r--
```

a:

```text
rw-rw-r--
```

En formato octal, el cambio es de `644` a `664`.

Ahora el propietario `devuser` y los integrantes del grupo `syslab` tienen permisos de lectura y escritura.

### **3.4.4 Comprobar la colaboración entre usuarios**

Volvemos a ingresar como `labuser`:

```bash
su - labuser
```

Y ejecutamos:

```bash
echo "Modificacion realizada por labuser" >> /opt/syslab/archivo-devuser.txt
```

La operación se realizó correctamente. Esto demuestra que `labuser` puede modificar un archivo creado por `devuser` cuando ambos pertenecen al grupo propietario y el archivo concede escritura al grupo.

El operador `>>` agrega texto al final del archivo sin reemplazar su contenido existente (A diferencia de `>`).

# **Conclusión de la práctica**

Durante esta práctica comprobamos que la colaboración entre usuarios en Linux depende de varios elementos:

- **Propietario y grupo propietario:** determinan qué categoría de permisos se aplica al usuario.
- **Permisos del directorio:** controlan si un usuario puede acceder al directorio y crear o eliminar entradas.
- **`setgid`:** permite que los archivos y subdirectorios nuevos hereden el grupo propietario del directorio.
- **`chmod`:** permite modificar los permisos de archivos y directorios.
- **Permisos del archivo:** determinan si un usuario puede leerlo o modificarlo una vez que tiene acceso a él.

También observamos que `setgid` no garantiza que los archivos nuevos tengan escritura grupal. Para configurar correctamente un directorio compartido, debemos considerar cómo se crean los archivos y qué permisos reciben inicialmente.

## **3.5 Umask y su configuracion**

Antes de empezar esta seccion, nos es conveniente juntar los valores de los umask, esto lo hacemos con el comando antes mostrado, realizando esto con los tres usuarios, empezando desde el usuario principal, osea:

``` bash
whoami
    lucacoss
umask
    0002
su - labuser
umask
    0002
su - devuser
umask
    0002
```

Bueno, ahora si, vamos a estudiar cómo se relaciona la `umask` con nuestro directorio compartido `/opt/syslab`.

### **3.5.1 ¿Qué significa umask 0002?**

La `umask` determina qué permisos se excluyen de los permisos iniciales solicitados al crear archivos y directorios.

El valor se expresa en octal. El último dígito, 2, excluye el permiso de escritura para otros usuarios.

Podemos observar su efecto con estos ejemplos:

| Tipo de recurso | Permisos | iniciales | Con umask 0002 | Resultado |
|---|---|---|---|---|
| Archivo regular |	666      | 0002      |	664 | `rw-rw-r--` |
| Directorio      |	777      | 0002      |	775 | `rwxrwxr-x` |

Estos son los resultados habituales para archivos regulares y directorios cuando los programas solicitan esos permisos iniciales. La `umask` no agrega permisos que el programa no haya solicitado.

#### **3.5.1 Creación de un archivo**

Ahora ingresamos como `devuser` y csudoreamos un archivo dentro del directorio compartido:

```bash
touch /opt/syslab/prueba-umask.txt
```

Y verificamos sus permisos:

```bash
ls -l /opt/syslab/prueba-umask.txt
    -rw-rw-r-- 1 devuser syslab 0 Oct 9 14:33 /opt/syslab/prueba-umask.txt
```

El archivo tiene permisos `664`. Tanto el propietario como el grupo pueden leer y modificar el contenido, mientras que otros usuarios solo pueden leerlo.

Además, el archivo pertenece al grupo `syslab`. Esto ocurre porque el directorio padre tiene activado el bit `setgid`.

#### **3.5.2 Creación de un subdirectorio**

Ahora creamos un subdirectorio:

```bash
mkdir /opt/syslab/directorio-umask
```

Verificamos sus permisos y grupo:

```bash
ls -ld /opt/syslab/directorio-umask
    drwxrwsr-x 2 devuser syslab 4096 Oct 9 14:34 /opt/syslab/directorio-umask
```

El subdirectorio tiene permisos `2775`: el propietario y el grupo disponen de permisos completos, mientras que otros usuarios pueden leer y atravesar el directorio.

La `s` en los permisos del grupo indica que el bit `setgid` también está activo en el subdirectorio. Este comportamiento permite mantener la herencia del grupo en los niveles siguientes del directorio compartido.

En esta práctica comprobamos que `umask` y `setgid` cumplen funciones diferentes y complementarias:

- `umask` influye en los permisos iniciales de archivos y directorios.
- `setgid` permite heredar el grupo propietario del directorio padre.
- Los permisos resultantes determinan qué operaciones pueden realizar los distintos usuarios.

La combinación de ambos mecanismos permite preparar directorios compartidos de manera más consistente, aunque todavía debemos comprobar cómo se comportan las modificaciones de archivos y los comandos ejecutados mediante `sudo`.

## **3.6 Prueba de colaboración entre usuarios**

Una vez configurado el directorio compartido `/opt/syslab`, se realizó una prueba para verificar que los usuarios `devuser` y `labuser` pudieran trabajar sobre un mismo archivo.

**Procedimiento**

Primero, se inició una sesión con `devuser` y se creó un archivo con contenido inicial:

```bash
su - devuser
echo "Creado por devuser" > /opt/syslab/prueba-colaboracion.txt
ls -l /opt/syslab/prueba-colaboracion.txt
```

Luego, se cambió a `labuser` y se agregó contenido al mismo archivo:

```bash
exit
su - labuser
echo "Modificado por labuser" >> /opt/syslab/prueba-colaboracion.txt
cat /opt/syslab/prueba-colaboracion.txt
```

Se verificó que ambos usuarios pudieron escribir sobre el mismo archivo y que el contenido agregado por cada uno se conservó.

**Análisis técnico**

El funcionamiento se debe a la combinación de varios mecanismos:

- El directorio `/opt/syslab` permite la escritura a los miembros del grupo `syslab`.
- El bit `setgid` hace que los archivos nuevos hereden el grupo `syslab`.
- La máscara `umask 0002`, en las sesiones utilizadas, permite que los archivos creados con permisos iniciales habituales tengan escritura para el grupo.
- Los permisos del archivo permiten que el segundo usuario agregue contenido sin ser su propietario.

**Conclusión**

La prueba demuestra cómo configurar un espacio de trabajo compartido en Linux para que distintos usuarios colaboren sobre los mismos archivos sin necesidad de utilizar la cuenta `root` para cada operación.