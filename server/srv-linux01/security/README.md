# Security

Documentación relacionada con la seguridad del servidor `srv-linux01`.

---

## Objetivos

Practicar conceptos básicos de seguridad en servidores Linux:

- Usuarios.
- Grupos.
- Permisos.
- Principio de mínimo privilegio.
- SSH.
- Firewall.
- Actualizaciones.
- Logs.
- Servicios activos.

---

## Usuarios

Se deberá evitar utilizar privilegios administrativos innecesariamente.

Las tareas administrativas deberán realizarse mediante `sudo` cuando corresponda.

---

## Permisos

Se deberán revisar periódicamente:

```bash
ls -l
```