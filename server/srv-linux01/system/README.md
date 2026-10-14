# Administración del sistema

Documentación relacionada con la administración del sistema operativo de `srv-linux01`.

---

## Objetivos

Practicar:

- Usuarios.
- Grupos.
- Permisos.
- Procesos.
- Servicios.
- systemd.
- Logs.
- Paquetes.
- Información del sistema.
- Administración básica.

---

# Administración del sistema

Previamente vale aclarar que mucho de todos estos comandos, directorios o conceptos se detalla en [docs](../../../docs/README.md)

## **Actualización del sistema**

Primero vamos a realizar una actualizacion de paquetes y librerias para que todo funcione correctamente. Esto lo podemos realizar con.

Actualización del sistema

``` bash
sudo apt update
```

![Respuesta del update](../../../screenshots/system/img01.png)

Y despues:

``` bash
sudo apt upgrade
```

![Respuesta del upgrade](../../../screenshots/system/img02.png)

---

Esto es lo principal asi como algo que se tendria que hacer de manera periodica, en un futuro vamos a realizar una automatización sobre esto con un script. 

El resto de procesos que realizaremos para levantar el servidor linux se documenta en el resto de archivos numerados!

## 5. Procesos

## 6. Servicios y systemd

## 7. Logs y journalctl

## 8. Gestión de paquetes

## 9. Información y diagnóstico del sistema

## 10. Tareas de administración

## 11. Problemas encontrados y soluciones