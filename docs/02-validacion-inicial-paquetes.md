# Validacion inicial de paquetes

Los comandos principales para actualizar los paquetes son

``` bash
sudo apt update
```

Y despues:

``` bash
sudo apt upgrade
```

Es importante ejercutarlos en ese orden para que la actualizacion se realice con exito.

Esto sucede porque el comando `sudo apt update` descarga la información más reciente de los repositorios configurados en el sistema para **actualizar la base de datos local** de paquetes disponibles y sus versiones, pero ***no*** instala ni actualiza ningún programa en la compu.

En caso de encontrar paquetes y actualizar la base de datos local, entonces ejecutamos `sudo apy upgrade` que instala las versiones más recientes de todos estos paquetes ya instalados en la compu, basándose en la **base de datos local**.