# Práctica 6

Najlae Metaich 

#### Los objetivos concretos de esta práctica son:
- Configurar dos discos en RAID 1. Los discos se añadirán a un sistema ya
instalado y funcionando, de forma que en total tendremos tres discos (el
sistema operativo estará ya instalado en /dev/sda y añadiremos dos discos
más, que serán el /dev/sdb y el /dev/sdc, para configurar el dispositivo de
almacenamiento RAID en estos dos discos nuevos de igual tamaño).
- Hacer pruebas de retirar y añadir un disco “en caliente”, y comprobar que el
RAID sigue funcionando correctamente.

###  Configuración del RAID por sofware

Tras crear los dos discos duros en la configuración de la máquina, la arrancamos e instalamos el software para configrurar el RAID: 

```sh
sudo apt-get install mdadm
```

Buscamos los identificadores de ambos discos con:
```sh
sudo fdisk -l
```
![alt tag](https://github.com/NajMetaich/SWAP16/blob/master/Pr%C3%A1cticas/Pr%C3%A1ctica6/idenfiticadores%20discos.PNG?raw=true)

Ahora creamos el RAID1. 
```sh
sudo mdadm -C /dev/md0 --level=raid1 --raid-devices=2 /dev/sdb /dev/sdc
```
![alt tag](https://github.com/NajMetaich/SWAP16/blob/master/Pr%C3%A1cticas/Pr%C3%A1ctica6/creacion%20raid1.PNG?raw=true)

Le damos formato mediante: 
```sh
sudo mkfs /dev/md0
```
y procedemos a crear el directorio en el que se montará el raid.

```sh
sudo mkdir /dat
sudo mount /dev/md0 /dat
```

comprobamos con ``sudo mount`` y vemos que se ha hecho con éxito. 
![alt tag](https://github.com/NajMetaich/SWAP16/blob/master/Pr%C3%A1cticas/Pr%C3%A1ctica6/crearunidadraid.PNG?raw=true)

Ahora comprobamos el estado del RAID mediante:
```sh
sudo mdadm --detail /dev/md0
```


Obtenemos los UUID de los dispositivos de almacenamiento 
``ls -l /dev/disk/by-uuid/``
![alt tag](https://github.com/NajMetaich/SWAP16/blob/master/Pr%C3%A1cticas/Pr%C3%A1ctica6/uuid.PNG?raw=true)

Añadimos esto en /etc/fstab`
```sh
UUID=57d229f4-2a48-4262-9b9b-34d165ffd3c1 /dat ext2 defaults 0 0
```

Simulamos un fallo con: 
```sh
sudo mdadm --manage --set-faulty /dev/md0 /dev/sdb
```
![alt tag](https://github.com/NajMetaich/SWAP16/blob/master/Pr%C3%A1cticas/Pr%C3%A1ctica6/simulacionfallo.PNG?raw=true)

![alt tag](https://github.com/NajMetaich/SWAP16/blob/master/Pr%C3%A1cticas/Pr%C3%A1ctica6/funcionamientoraid.PNG?raw=true)




