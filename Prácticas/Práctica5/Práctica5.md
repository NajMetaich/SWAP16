# Práctica 4

Najlae Metaich 

#### Los objetivos concretos de esta práctica son:
- Copiar archivos de copia de seguridad mediante ssh.
- Clonar manualmente BD entre máquinas.
- Configurar la estructura maestro-esclavo entre dos máquinas para realizar el
clonado automático de la información.

### Crear una BD e insertar datos

Para llevar a cabo la práctica crearé una BD llamada contacos e insertaré en ella algunos datos. 
Primero accederé a la interfaz de comandos de MySQL a través de:

```sh
mysql -uroot -p
```
Luego crearé la base de datos "contactos"
```sh
mysql> create database contactos;
```
Luego creo una tabla llamada personas donde meteré unas cuantas tuplas de tal forma que quede como se muestra en la imagen: 

![alt tag](https://github.com/NajMetaich/SWAP16/blob/master/Pr%C3%A1cticas/Pr%C3%A1ctica5/mysql.PNG?raw=true)

### Replicar una BD MySQL con mysqldump
Para realizar el backup de la BD primero debemos bloquear el acceso a la BD mediante:
```sh 
FLUSH TABLES WITH READ LOCK;
```
Ahora realizamos la copia mediante mysqldump:

```sh
mysqldump contactos -u root -p > /root/contactos.sql
```

Hacemos un cat al sql generado y nos sale algo parecido a esta captura: 
![alt tag](https://github.com/NajMetaich/SWAP16/blob/master/Pr%C3%A1cticas/Pr%C3%A1ctica5/backup.PNG?raw=true)

Finalmente desbloqueamos la base de datos mediante: 
```sh
 UNLOCK TABLES;
```
Y ya podemos acceder a la máquina 2 para copiar el archivo .sql con todos los datos guardados en la máquina 1 mediante: 

```sh
scp root@192.168.227.128:/root/contactos.sql /root/
```
Y ya podemos importar la BD completa en el MySQL mediante: 

```sh
mysql -u root –p
mysql> CREATE DATABASE ‘contactos’;
mysql> quit
mysql -u root -p contactos < /root/contactos.sql
```

###  Replicación de BD mediante una configuración maestro-esclavo
Lo primero que debemos hacer es la configuración de mysql del maestro. Para ello
editamos, como root, el /etc/mysql/my.cnf para realizar las modificaciones que se
describen a continuación.

Comentamos el parámetro bind-address que sirve para que escuche a un servidor:
#bind-address 127.0.0.1

Le indicamos el archivo donde almacenar el log de errores. 

Establecemos el identificador del servidor. server-id = 1

Guardamos el documento y reiniciamos el servicio:

```sh
/etc/init.d/mysql restart
```
Pasamos a la segunda máquina y volvemos a editar el fichero /etc/mysql/my.cnf. Pondremos lo mismo que en el anterior salvo server-id que será 2.

Reiniciamos el servicio en el esclavo:
```sh
/etc/init.d/mysql restart
```
Entramos en la primera máquina para crear un usuario que tenga permisos para replicación mediante lo siguiente: 

```sh
CREATE USER esclavo IDENTIFIED BY 'esclavo';
GRANT REPLICATION SLAVE ON *.* TO 'esclavo'@'%' IDENTIFIED BY 'esclavo';
FLUSH PRIVILEGES;
FLUSH TABLES;
FLUSH TABLES WITH READ LOCK;
SHOW MASTER STATUS;
```
![alt tag](https://github.com/NajMetaich/SWAP16/blob/master/Pr%C3%A1cticas/Pr%C3%A1ctica5/automatizar.PNG?raw=true)

Tras esto accedemos a la segunda máquina (esclavo) e introducimos lo siguiente con la IP del maestro: 

```sh
CHANGE MASTER TO MASTER_HOST='192.168.227.128', MASTER_USER='esclavo', MASTER_PASSWORD='esclavo', MASTER_LOG_FILE='mysql-bin.000002', MASTER_LOG_POS=501, MASTER_PORT=3306;
```

Ahora activamos el esclavo con ``` start slave: ``` 

![alt tag](https://github.com/NajMetaich/SWAP16/blob/master/Pr%C3%A1cticas/Pr%C3%A1ctica5/start%20esclavo.PNG?raw=true)

Ahora, como última prueba, crearé alguna tabla e insertaremos varias tuplas en la máquina maestro y comprobaremos que se replican en el esclavo.

![alt tag](https://github.com/NajMetaich/SWAP16/blob/master/Pr%C3%A1cticas/Pr%C3%A1ctica5/sincronizacion.PNG?raw=true)







