# Auditoría de registros de autenticación SSH

## 1. Objetivo

Analizar los registros generados por el servidor SSH de `srv-linux01` para identificar conexiones exitosas, aperturas y cierres de sesión, y comprender cómo utilizar `journalctl` y los archivos de registro para investigar eventos de autenticación.

## 2. Entorno

- **Servidor:** `srv-linux01`
- **Sistema operativo:** Ubuntu Server
- **Usuario administrado:** `lucacoss`
- **Servicio:** OpenSSH Server
- **Puerto SSH en el servidor:** TCP 22
- **Acceso desde Windows:** `127.0.0.1:2222`, mediante red NAT y reenvío de puertos de VirtualBox
- **Método de autenticación:** clave pública Ed25519

## 3. Fuentes de registros

### 3.1. Archivo `/var/log/auth.log`

Se verificó la existencia del archivo mediante:

```bash
sudo ls -lh /var/log/auth.log
```

El archivo se encontraba disponible y presentaba permisos `640`, con propietario `syslog` y grupo `adm`.

Este archivo contiene eventos relacionados con la autenticación y las sesiones, incluidos registros generados por SSH y otros mecanismos de autenticación del sistema.

Para consultar eventos relacionados con SSH:

```bash
sudo grep -E 'sshd|Accepted|Failed|Invalid user' /var/log/auth.log | tail -n 20
```

El filtro permite localizar líneas relevantes, pero los resultados deben interpretarse individualmente. Las palabras `Failed` o `Accepted`, por sí solas, no identifican necesariamente un evento de autenticación SSH.

### 3.2. Registros de `systemd` mediante `journalctl`

Para consultar eventos del servicio SSH:

```bash
sudo journalctl -u ssh.service -n 30 --no-pager
```

Para consultar eventos del socket:

```bash
sudo journalctl -u ssh.socket -n 20 --no-pager
```

Para investigar eventos recientes relacionados con SSH:

```bash
sudo journalctl --since "5 minutes ago" --no-pager | grep -E 'sshd-session|sshd\['
```

El parámetro `--since` limita la consulta a un intervalo temporal. El filtro posterior ayuda a localizar los registros de los procesos SSH.

Los resultados dependen del momento de ejecución del comando y de los eventos que se hayan producido durante el intervalo consultado.

## 4. Interpretación de eventos

### 4.1. Inicio del servidor

Se encontraron registros similares a:

```text
Server listening on 0.0.0.0 port 22.
Server listening on :: port 22.
```

Estos mensajes indican que el servidor SSH comenzó a escuchar en el puerto 22 mediante IPv4 e IPv6, respectivamente.

### 4.2. Autenticación exitosa por clave pública

Se observó el siguiente tipo de evento:

```text
Accepted publickey for lucacoss from 10.0.2.2 port 51453 ssh2: ED25519 SHA256:...
```

Este mensaje confirma que SSH aceptó la autenticación mediante clave pública para el usuario `lucacoss`.

La dirección `10.0.2.2` corresponde al origen que observa el servidor dentro de la configuración NAT utilizada por la máquina virtual.

El número de puerto que aparece después de la dirección es un puerto de origen de la conexión del cliente; no es el puerto de escucha SSH del servidor.

### 4.3. Apertura de sesión

También se registró:

```text
pam_unix(sshd:session): session opened for user lucacoss(uid=1000) by lucacoss(uid=0)
```

PAM registró la apertura de la sesión para el usuario `lucacoss`, cuyo UID es `1000`.

El UID `0` corresponde al contexto privilegiado del proceso que abrió la sesión; no implica que el usuario remoto haya iniciado sesión como root.

### 4.4. Cierre de sesión

Después de ejecutar `exit` desde la conexión remota, se encontraron eventos equivalentes a:

```text
Received disconnect from 10.0.2.2 port 51453:11: disconnected by user
Disconnected from user lucacoss 10.0.2.2 port 51453
pam_unix(sshd:session): session closed for user lucacoss
```

Estos registros son consistentes con una desconexión iniciada por el cliente y el posterior cierre de la sesión.

### 4.5. Mensaje de logout

Durante el análisis también apareció:

```text
syslogin_perform_logout: logout() returned an error
```

Este mensaje se observó junto con otros registros de cierre de sesión. Por sí solo no demuestra una vulnerabilidad ni permite concluir que la sesión haya quedado abierta.

Se conserva como un hallazgo que podría investigarse si se repite en un contexto asociado a fallos reales. No se realizaron cambios de configuración basados únicamente en este mensaje.

## 5. Diferencia entre `ssh.service` y `ssh.socket`

Los registros de `ssh.socket` muestran eventos de escucha, activación y desactivación del socket administrado por `systemd`.

La unidad `ssh.socket` y la unidad `ssh.service` cumplen funciones diferentes. Por eso, un evento de actividad del socket no equivale a una autenticación y la ausencia de nuevos eventos en una unidad no demuestra, por sí sola, que SSH esté fallando.

Para evaluar el estado actual del servicio y del socket se pueden utilizar:

```bash
systemctl status ssh.service --no-pager
systemctl status ssh.socket --no-pager
```

El estado actual debe interpretarse junto con la configuración y los registros disponibles.

## 6. Resultado de la práctica

Se verificaron los siguientes puntos:

- Existencia y permisos del archivo `/var/log/auth.log`.
- Consulta de registros mediante `journalctl`.
- Identificación de mensajes de inicio del servidor SSH.
- Identificación de una autenticación exitosa por clave pública Ed25519.
- Identificación de la apertura de una sesión PAM.
- Identificación de la desconexión del cliente y del cierre de sesión.
- Diferenciación entre registros de SSH y mensajes `Failed` generados por otros componentes del sistema.
- Reconocimiento de un mensaje de logout que requiere contexto adicional antes de atribuirle un problema.

En las consultas realizadas no se identificaron intentos fallidos de autenticación SSH. Esto no demuestra que nunca hayan existido, sino que no se observaron en los resultados analizados.

## 7. Comandos de referencia

| Propósito | Comando |
|---|---|
| Verificar el archivo de autenticación | `sudo ls -lh /var/log/auth.log` |
| Filtrar eventos de autenticación | `sudo grep -E 'sshd|Accepted|Failed|Invalid user' /var/log/auth.log \| tail -n 20` |
| Consultar el servicio SSH | `sudo journalctl -u ssh.service -n 30 --no-pager` |
| Consultar el socket SSH | `sudo journalctl -u ssh.socket -n 20 --no-pager` |
| Consultar eventos recientes de SSH | `sudo journalctl --since "5 minutes ago" --no-pager \| grep -E 'sshd-session|sshd\['` |
