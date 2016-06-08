# Práctica 2

Najlae Metaich 

Los objetivos concretos de esta segunda práctica son:
- aprender a copiar archivos mediante ssh
- clonar contenido entre máquinas
- configurar el ssh para acceder a máquinas remotas sin contraseña
- establecer tareas en cron


 ## Automatizar SSH 
Lo que se pretende hacer es conectar las máquinas automáticamente mediante ssh, cosa que haré en ambas máquinas mediante: 

```sh
ssh-keygen -t dsa 
```
![alt tag](https://github.com/Najlaemt/SWAP1516/blob/master/Pr%C3%A1cticas/Pr%C3%A1ctica2/ssh1.PNG?raw=true)

Lo siguiente a hacer es mandar la clave pública a la otra máquina.

```sh
ssh-copy-id -i .ssh/id_dsa.pub root@192.168.227.129  
```
![alt tag](https://github.com/Najlaemt/SWAP1516/blob/master/Pr%C3%A1cticas/Pr%C3%A1ctica2/ssh11.PNG?raw=true)

Ahora comprobamos su funcionamiento mediante 

```sh 
ssh root@192.168.227.129 y si está bien hecho no debería de pedir contraseña.
```
![alt tag](https://github.com/Najlaemt/SWAP1516/blob/master/Pr%C3%A1cticas/Pr%C3%A1ctica2/ssh111.PNG?raw=true)

Comprobamos manualmente con un fichero que se envía correctamente. 

![alt tag](https://github.com/Najlaemt/SWAP1516/blob/master/Pr%C3%A1cticas/Pr%C3%A1ctica2/ssh1111.PNG?raw=true)

A continuación haremos lo mismo pero con la máquina 2. 
## Sincronización con rsync
El siguiente paso es instalar rsync con sudo apt-get install rsync

Añadiremos para que se clonen las carpetas de la máquina primera a la segunda el siguiente comando: 

```sh
rsync -avz --delete --exclude=**/stats --exclude=**/error --exclude=**/files/pictures -e "ssh -l root" root@192.168.227.128:/var/www/ /var/www/
``` 
![alt tag](https://github.com/Najlaemt/SWAP1516/blob/master/Pr%C3%A1cticas/Pr%C3%A1ctica2/ssh2.PNG?raw=true)

## Automatizar con crontab
Ahora vamos a automatizar esta operación mediante crontab. Editamos el fichero /etc/crontab de la segunda máquina y añadimos: 

```sh 
*/5 * * * * root rsync -avz --delete --exclude=**/stats --exclude=**/error --exclude=**/files/pictures -e "ssh -l root" root@192.168.227.128:/var/www/ /var/www/
```
![alt tag](https://github.com/Najlaemt/SWAP1516/blob/master/Pr%C3%A1cticas/Pr%C3%A1ctica2/ssh22.PNG?raw=true)





