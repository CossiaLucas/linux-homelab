# VirtualBox

Esta sección contiene la documentación relacionada con la infraestructura virtualizada del Linux Homelab.

VirtualBox será utilizado como plataforma de virtualización para crear y administrar las máquinas virtuales del laboratorio.

---

## Objetivos

- Crear máquinas virtuales Linux.
- Configurar recursos de hardware virtual.
- Configurar almacenamiento virtual.
- Configurar adaptadores de red.
- Administrar snapshots cuando sea necesario.
- Documentar la configuración de cada VM.

---

## Máquina virtual inicial

La primera máquina virtual definida para el laboratorio es:

```text
Hostname: srv-linux01
OS: Ubuntu Server 26.04 LTS
CPU: 2 vCPU
RAM: 2 GB
Disk: 25 GB
Network: NAT
```