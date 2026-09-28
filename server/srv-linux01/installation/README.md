# Instalación de srv-linux01

Documentación correspondiente a la instalación del sistema operativo en `srv-linux01`.

---

## Objetivo

Instalar Ubuntu Server 26.04 LTS en una máquina virtual de VirtualBox y dejar preparado el servidor para las siguientes etapas del Homelab.

---

## Entorno

| Parámetro | Valor |
|---|---|
| Virtualizador | VirtualBox |
| VM | `srv-linux01` |
| OS | Ubuntu Server 26.04 LTS |
| Arquitectura | amd64 |
| CPU | 2 vCPU |
| RAM | 2 GB |
| Disco | 25 GB |
| Red | NAT |

---

## Procedimiento

La instalación deberá documentarse siguiendo los pasos realizados realmente durante la instalación.

Como mínimo se deberá registrar:

- Creación de la máquina virtual.
- Configuración de CPU.
- Configuración de RAM.
- Creación del disco virtual.
- Configuración de red.
- Montaje de la ISO.
- Inicio del instalador.
- Configuración regional.
- Configuración del teclado.
- Configuración de red.
- Configuración del hostname.
- Creación del usuario.
- Configuración del almacenamiento.
- Instalación del sistema.
- Instalación del bootloader.
- Primer inicio.

---

## Verificación

Después de la instalación se deberán comprobar:

```bash
hostnamectl
```