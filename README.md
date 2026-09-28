# Linux Homelab

Laboratorio personal de infraestructura orientado al aprendizaje práctico de Linux, administración de sistemas, redes, seguridad, automatización y servicios.

Este repositorio documenta la construcción y evolución de un entorno de laboratorio virtualizado utilizando VirtualBox y servidores Linux.

## Objetivos

El objetivo principal de este Homelab es transformar los conocimientos adquiridos durante la formación en Linux en prácticas reproducibles y documentadas.

Los principales objetivos son:

- Administrar servidores Linux.
- Comprender la configuración y administración del sistema.
- Trabajar con usuarios, grupos y permisos.
- Administrar servicios y procesos.
- Configurar redes en entornos Linux.
- Utilizar SSH para administración remota.
- Implementar reglas básicas de firewall.
- Analizar logs y realizar troubleshooting.
- Implementar servicios de infraestructura.
- Automatizar tareas mediante Bash.
- Incorporar contenedores con Docker.
- Introducir automatización de infraestructura con Ansible.
- Implementar herramientas de monitoreo.
- Documentar procedimientos técnicos de manera reproducible.

---

## Tecnologías y herramientas

### Virtualización

- VirtualBox

### Sistemas operativos

- Ubuntu Server
- Debian

### Administración Linux

- Bash
- systemd
- APT
- SSH
- UFW
- herramientas GNU/Linux

### Servicios

- Nginx
- Docker
- Samba
- servicios de red

### Automatización

- Bash
- Ansible

### Monitoreo

- Prometheus
- Grafana

> Las tecnologías se incorporarán progresivamente a medida que avance el laboratorio.

---

## Arquitectura inicial

El laboratorio comienza con una única máquina virtual Linux:

```text
                    HOST
                     │
                  VirtualBox
                     │
                     ▼
              ┌───────────────┐
              │ srv-linux01   │
              │ Ubuntu Server │
              │               │
              │ 2 vCPU        │
              │ 2 GB RAM      │
              │ 25 GB Disk    │
              └───────────────┘
                     │
                     │ NAT
                     ▼
                  Internet
```
