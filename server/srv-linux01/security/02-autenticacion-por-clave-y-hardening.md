# Endurecimiento de SSH: autenticación por clave pública

## 1. Objetivo

Configurar el acceso remoto a `srv-linux01` para que el usuario `lucacoss` pueda autenticarse mediante una clave SSH, **deshabilitar** la autenticación SSH por contraseña y por teclado interactivo, y verificar que el acceso siga funcionando.

## 1.1 Estado

**Resultado:** la configuración se validó y una nueva conexión desde PowerShell funcionó correctamente utilizando la clave privada correspondiente. Dentro de la sesión remota, `hostname` devolvió `srv-linux01` y `whoami` devolvió `lucacoss`.

## 2. Arquitectura de la conexión

```text
Windows (equipo anfitrión)
    |
    | Cliente SSH: 127.0.0.1:2222
    v
Regla NAT de VirtualBox
    |
    | TCP 2222 -> 10.0.2.15:22
    v
Ubuntu Server: srv-linux01
    |
    | Usuario: lucacoss
    | Autenticación: clave pública
    v
Sesión remota
```

Estos fueron algunos valores observados durante esta práctica:

- Servidor: `srv-linux01`.
- Usuario remoto: `lucacoss`.
- Dirección IPv4 de la VM: `10.0.2.15`.
- Puerto SSH del invitado: `22/TCP`.
- Dirección local utilizada desde Windows: `127.0.0.1`.
- Puerto del anfitrión: `2222/TCP`.
- Clave privada en Windows: `%USERPROFILE%\.ssh\id_ed25519_srv-linux01`.
- Clave pública correspondiente: `%USERPROFILE%\.ssh\id_ed25519_srv-linux01.pub`.

## 3. Conceptos principales

Revisalos en [docs](../../../docs/05-redes.md)

## 4. Preparación y permisos de la clave pública

La clave se generó en Windows con `ssh-keygen`, usando el algoritmo Ed25519 y el nombre `id_ed25519_srv-linux01`. La clave privada quedó en Windows y la clave pública se copió al servidor.

En Ubuntu, se preparó el directorio y el archivo de claves del usuario `lucacoss`:

```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
nano ~/.ssh/authorized_keys #Aca se copio la clave publica del usuario en windows
chmod 600 ~/.ssh/authorized_keys
chown -R lucacoss:lucacoss ~/.ssh
```

Se cambiaron los permisos para tener esto:

| Ruta | Permisos | Significado |
|---|---|---|
| `~/.ssh` | `700` (`drwx------`) | Solo el propietario puede listar, leer y acceder al directorio. |
| `~/.ssh/authorized_keys` | `600` (`-rw-------`) | Solo el propietario puede leer y modificar el archivo. |

Estos permisos reducen la posibilidad de que otros usuarios del sistema lean o modifiquen las claves autorizadas. Osea mas **seguridad** :)

## 5. Estado inicial de la configuración SSH

Se comprobó que `openssh-server` estaba instalado. El estado de las unidades de systemd mostró:

- `ssh.socket`: `active (listening)` y `enabled`.
- `ssh.service`: inicialmente aparecía como `inactive (dead)` y `disabled`.
- El puerto TCP 22 estaba en escucha y aparecía asociado a `systemd`.

Esta situación es compatible con la activación por socket: systemd escucha en el puerto configurado y puede activar el servicio SSH cuando llega una conexión. No debe interpretarse automáticamente que SSH está averiado solo porque `ssh.service` aparezca inactivo antes de recibir conexiones.

Se revisó la configuración efectiva con:

```bash
sudo sshd -T | grep -E '^(pubkeyauthentication|passwordauthentication|kbdinteractiveauthentication|permitrootlogin|authorizedkeysfile) '
```

Antes del cambio, los valores relevantes eran:

```text
permitrootlogin prohibit-password
pubkeyauthentication yes
passwordauthentication yes
authorizedkeysfile .ssh/authorized_keys .ssh/authorized_keys2
```

La consulta específica posterior confirmó:

```text
kbdinteractiveauthentication no
```

Por lo tanto, la autenticación por clave pública estaba habilitada, pero la autenticación SSH por contraseña también estaba permitida.

## 6. Identificación del archivo que permitía contraseñas

Se inspeccionaron las directivas de configuración:

```bash
sudo grep -nEi '^[[:space:]]*(Include|PubkeyAuthentication|PasswordAuthentication|KbdInteractiveAuthentication|PermitRootLogin|Match)' /etc/ssh/sshd_config
```

```bash
sudo grep -nHEi '^[[:space:]]*(PubkeyAuthentication|PasswordAuthentication|KbdInteractiveAuthentication|PermitRootLogin|Match)' /etc/ssh/sshd_config.d/*.conf
```

La revisión mostró que:

- `/etc/ssh/sshd_config` incluía los archivos de `/etc/ssh/sshd_config.d/`.
- `KbdInteractiveAuthentication no` ya estaba configurado en el archivo principal.
- `/etc/ssh/sshd_config.d/50-cloud-init.conf` contenía `PasswordAuthentication yes`.

El objetivo fue establecer una configuración local explícita sin modificar el archivo generado por `cloud-init`.

## 7. Configuración local de endurecimiento

Se creó el archivo:

```text
/etc/ssh/sshd_config.d/00-local-hardening.conf
```

Con este contenido:

```text
PubkeyAuthentication yes
PasswordAuthentication no
KbdInteractiveAuthentication no
```

Se eligió el prefijo `00-` para que el archivo se procese antes que `50-cloud-init.conf` cuando el directorio se incluye mediante un patrón ordenado. OpenSSH normalmente utiliza el primer valor obtenido para una directiva; por eso el orden de lectura es relevante.

El archivo `50-cloud-init.conf` se dejó intacto. Esto evita modificar directamente una configuración que puede ser administrada por `cloud-init`.

## 8. Validación de sintaxis y configuración efectiva

Antes de probar una conexión nueva, se validó la sintaxis:

```bash
sudo sshd -t
```

El comando no mostró salida ni errores, lo que indicó que la configuración podía validarse correctamente.

Después se consultaron los valores efectivos:

```bash
sudo sshd -T | grep -E '^(pubkeyauthentication|passwordauthentication|kbdinteractiveauthentication|permitrootlogin) '
```

Resultado obtenido:

```text
permitrootlogin prohibit-password
pubkeyauthentication yes
passwordauthentication no
kbdinteractiveauthentication no
```

Interpretación:

- `pubkeyauthentication yes`: continúa habilitada la autenticación por clave pública.
- `passwordauthentication no`: la autenticación SSH por contraseña está deshabilitada.
- `kbdinteractiveauthentication no`: la autenticación interactiva de teclado está deshabilitada.
- `permitrootlogin prohibit-password`: `root` no puede iniciar sesión por SSH mediante contraseña; esta directiva no significa que toda forma de acceso de `root` esté deshabilitada.

La validación con `sshd -t` y la inspección con `sshd -T` se complementan: la primera revisa la sintaxis y la segunda muestra los valores efectivos interpretados por el servidor.

## 9. Prueba final desde Windows

Se mantuvo abierta la sesión SSH anterior como vía de recuperación y se abrió una nueva ventana de PowerShell.

Se ejecutó:

```powershell
ssh -o PreferredAuthentications=publickey -o PasswordAuthentication=no -o KbdInteractiveAuthentication=no -i "$env:USERPROFILE\.ssh\id_ed25519_srv-linux01" -p 2222 lucacoss@127.0.0.1
```

Significado de las opciones:

- `-o PreferredAuthentications=publickey`: prioriza la autenticación mediante clave pública.
- `-o PasswordAuthentication=no`: impide que ese cliente utilice autenticación por contraseña.
- `-o KbdInteractiveAuthentication=no`: impide que ese cliente utilice autenticación interactiva de teclado.
- `-i`: indica qué archivo de clave privada utilizar.
- `-p 2222`: conecta al puerto 2222 del anfitrión.
- `lucacoss@127.0.0.1`: intenta iniciar sesión como `lucacoss` a través del puerto local redirigido por VirtualBox.

La prueba fue exitosa. Dentro de la nueva sesión remota, los comandos:

```bash
hostname
    srv-linux01
whoami
    lucacoss
```

Esto confirmó que la conexión continuó funcionando después del endurecimiento y que se pudo iniciar sesión con el usuario esperado.

Las opciones `PasswordAuthentication=no` y `KbdInteractiveAuthentication=no` en el comando del cliente limitan los métodos que intenta ese cliente. La comprobación del lado del servidor es la salida de `sshd -T`, que confirmó que ambos métodos están deshabilitados en la configuración efectiva del servidor.

## 10. Seguridad y recuperación

- Mantener la clave privada únicamente en Windows; nunca subirla al repositorio.
- No es necesario publicar el contenido de la clave pública en el repositorio.
- No compartir la passphrase de la clave privada ni contraseñas.
- Conservar una vía de recuperación, como la consola de VirtualBox, al modificar la autenticación SSH.
- Antes de cambios futuros, validar la sintaxis con `sudo sshd -t` y revisar los valores efectivos con `sudo sshd -T`.
- No modificar `50-cloud-init.conf` sin una razón concreta; la configuración local se separó para evitar editar directamente ese archivo.
- `PermitRootLogin prohibit-password` restringe el acceso de `root` por contraseña, pero no prohíbe necesariamente todas las formas de acceso SSH de `root`. La política puede revisarse en una práctica posterior si el diseño del homelab lo requiere.
