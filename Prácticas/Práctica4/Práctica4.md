# Práctica 4

Najlae Metaich 

- En esta práctica se deben usar las dos herramientas para medir, primero el rendimiento de una sola máquina servidora y a continuación el rendimiento de la granja web cuando usamos balanceo de carga tanto con nginx como con haproxy (haciendo las peticiones a la dirección IP del balanceador).
- Mediante el uso de un script en PHP se realizan varios cálculos para que se requiera una mayor cantidad de tiempo a la hora de ofrecer la respuesta de la petición. 


### Comprobar el rendimiento de servidores web con Apache Benchmark
Lo primero que se hará será usar Apache Benchmark para ver el rendimiento del servidor. Usaré este comando para ello: 
En el primer caso, que es sin ningún balanceador, se pone la IP correspondiente a mi primera máquina. Luego, para realizar la misma prueba pero con Nginx o HAProxy activados pondré la IP de la máquina 3. 



```sh
ab -n 1000 -c 10 http://192.168.227.128/
ab -n 1000 -c 10 http://192.168.227.131/

``` 
Dando como resultados los que se muestran recopilados en las siguientes tablas con sus correspondientes gráficas: 

![alt tag](https://github.com/NajMetaich/SWAP16/blob/master/Pr%C3%A1cticas/Pr%C3%A1ctica4/tablas%20ab.PNG?raw=true)

![alt tag](https://github.com/NajMetaich/SWAP16/blob/master/Pr%C3%A1cticas/Pr%C3%A1ctica4/ab01.PNG?raw=true)

![alt tag](https://github.com/NajMetaich/SWAP16/blob/master/Pr%C3%A1cticas/Pr%C3%A1ctica4/ab02.PNG?raw=true)

### Comprobar el rendimiento con Siege

El segundo será usando Siege, y haré el mismo procedimiento. Primero solo el servidor, luego con nginx activado y haproxy desactivado y viceversa con los siguientes comandos: 

```sh
sudo siege -b -t40S -c 10 -v http://192.168.227.128/ 
sudo siege -b -t40S -c 10 -v http://192.168.227.131/
```

![alt tag](https://github.com/NajMetaich/SWAP16/blob/master/Pr%C3%A1cticas/Pr%C3%A1ctica4/siege1.PNG?raw=true)

![alt tag](https://github.com/NajMetaich/SWAP16/blob/master/Pr%C3%A1cticas/Pr%C3%A1ctica4/siege2.PNG?raw=true)

![alt tag](https://github.com/NajMetaich/SWAP16/blob/master/Pr%C3%A1cticas/Pr%C3%A1ctica4/siege01.PNG?raw=true)

![alt tag](https://github.com/NajMetaich/SWAP16/blob/master/Pr%C3%A1cticas/Pr%C3%A1ctica4/siege02.PNG?raw=true)
![alt tag](https://github.com/NajMetaich/SWAP16/blob/master/Pr%C3%A1cticas/Pr%C3%A1ctica4/siege03.PNG?raw=true)

Puedo deducir a partir de los resultados que tanto Nginx como HAProxy muestran unos resultados similares por tanto sería una opción viable cualquiera de los dos. En último caso, se podría tener un único servidor Apache instalado. 





