# Especificaciones de máquinas virtuales

Este documento registra las especificaciones de las máquinas virtuales utilizadas en el Linux Homelab.

---

## srv-linux01

### Identificación

| Parámetro | Valor |
|---|---|
| Hostname | `srv-linux01` |
| Sistema operativo | Ubuntu Server 26.04 LTS |
| Arquitectura | amd64 |
| Virtualizador | VirtualBox |

### Recursos

| Recurso | Valor |
|---|---|
| CPU | 2 vCPU |
| RAM | 2 GB |
| Disco | 25 GB |

### Red

| Parámetro | Valor |
|---|---|
| Adaptador inicial | NAT |
| Objetivo | Acceso inicial a Internet |

### Estado

**Planificado / pendiente de implementación.**

La configuración real deberá verificarse después de crear la VM.

---

## Futuras máquinas

Las siguientes máquinas no están implementadas actualmente.

Podrán incorporarse cuando las prácticas del laboratorio lo requieran.

### srv-linux02

Posible función:

- Servicios.
- Docker.
- Infraestructura.

### srv-linux03

Posible función:

- Monitoring.
- Prometheus.
- Grafana.

Estas funciones son propuestas y podrán modificarse según las necesidades del laboratorio.