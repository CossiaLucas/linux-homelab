# 1. Autenticación por clave pública

SSH permite autenticar a un usuario mediante un par de claves criptográficas:

- **Clave privada:** permanece en el equipo cliente, en este caso Windows. **No se debe compartir ni copiar al servidor.**
- **Clave pública:** se instala en el servidor, dentro del archivo `~/.ssh/authorized_keys` del usuario que podrá iniciar sesión.

Durante la autenticación, el cliente demuestra que posee la clave privada correspondiente a una clave pública autorizada. La clave privada no se envía al servidor.

# 2. Passphrase y contraseña de la cuenta

Los explico pues son dos conceptos diferentes:

- La **contraseña de la cuenta** es la credencial del usuario `lucacoss` en Ubuntu.
- La **passphrase** protege localmente el archivo de clave privada en Windows, si se configuró una.

Por eso, al conectarse con una clave protegida, el cliente puede pedir la passphrase aunque la autenticación SSH por contraseña esté deshabilitada en el servidor.

# 3. Métodos de autenticación de OpenSSH

| Directiva | Función |
|---|---|
| `PubkeyAuthentication` | Permite o impide la autenticación por clave pública. |
| `PasswordAuthentication` | Controla la autenticación SSH mediante contraseña. |
| `KbdInteractiveAuthentication` | Controla el método de autenticación interactiva de teclado. Puede involucrar contraseñas u otros mecanismos configurados. |
| `PermitRootLogin` | Define si `root` puede iniciar sesión directamente por SSH y bajo qué condiciones. |
| `AuthorizedKeysFile` | Define las ubicaciones donde SSH busca las claves públicas autorizadas de un usuario. |

> Deshabilitar `PasswordAuthentication` no equivale a deshabilitar todos los métodos posibles de autenticación. Por eso también se revisó `KbdInteractiveAuthentication`.