# Architecture Decisions

Registro de decisiones importantes tomadas durante la construcción del Linux Homelab.

---

## ADR-001 — Virtualización con VirtualBox

### Decisión

Utilizar VirtualBox como plataforma inicial de virtualización.

### Motivo

Permite crear máquinas virtuales Linux de manera sencilla y controlada, facilitando la experimentación con diferentes configuraciones.

---

## ADR-002 — Primer servidor con Ubuntu Server

### Decisión

Utilizar Ubuntu Server 26.04 LTS como sistema operativo inicial del Homelab.

### Motivo

El servidor será utilizado como entorno práctico independiente del entorno utilizado para estudiar los contenidos introductorios de Linux.

---

## ADR-003 — Primera VM con recursos moderados

### Decisión

Configurar inicialmente:

- 2 vCPU.
- 2 GB RAM.
- 25 GB de almacenamiento.

### Motivo

El objetivo inicial es realizar prácticas de administración Linux y servicios básicos sin requerir una gran cantidad de recursos.

---

## ADR-004 — Red NAT inicial

### Decisión

Utilizar NAT como configuración de red inicial.

### Motivo

Permite proporcionar conectividad a Internet a la VM sin introducir inicialmente una arquitectura de red más compleja.

---

## ADR-005 — Evolución incremental

### Decisión

Construir el Homelab progresivamente.

### Motivo

La infraestructura debe crecer junto con los conocimientos y necesidades del laboratorio.

La evolución prevista incluye:

```text
Linux
→ Networking
→ SSH
→ Security
→ Services
→ Automation
→ Docker
→ Monitoring
→ Ansible
```