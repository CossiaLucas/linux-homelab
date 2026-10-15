# srv-linux01

Servidor Linux principal del Homelab.

---

## Información

| Parámetro | Valor |
|---|---|
| Hostname | `srv-linux01` |
| Sistema operativo | Ubuntu Server 26.04 LTS |
| Arquitectura | amd64 |
| CPU | 2 vCPU |
| RAM | 2 GB |
| Disco | 25 GB |
| Virtualizador | VirtualBox |
| Red inicial | NAT |

---

## Objetivos ya alcanzados

* [Instalacion de srv-linux01](./server/srv-linux01/installation/README.md)

* [Se levantaron nuevos usuarios](.\server\srv-linux01\system\01-usuarios-grupos.md)

* [Se verificaron permisos](.\server\srv-linux01\system\02-permisos.md)

* [Se verificaron algunos directorios](.\server\srv-linux01\system\03-directorios.md)

## Objetivos futuros

El servidor será utilizado para practicar:

- Networking.
- SSH.
- Firewall.
- Servicios web.
- Backups.
- Automatización.

---

## Estructura

```text
srv-linux01/
├── README.md
├── installation/
├── system/
├── networking/
├── security/
└── troubleshooting/
```