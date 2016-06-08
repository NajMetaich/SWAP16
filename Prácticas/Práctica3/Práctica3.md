# Práctica 3

Najlae Metaich 

**El objetivo concreto de esta segunda práctica son:**
- *Instalar los balanceadores nginx y haproxy con sus configuraciones round-robin y ponderación.*

Lo que se pretende hacer es instalar los balanceadores de carga **Nginx y haproxy**.
## Balanceador nginx
Empezaremos por Nginx introduciendo lo siguiente en la máquina 3: 

```sh
cd /tmp/
wget http://nginx.org/keys/nginx_signing.key
apt-key add /tmp/nginx_signing.key
rm -f /tmp/nginx_signing.key
``` 

Ahora añadimos los repositorios de Nginx modificando el fichero

/etc/apt/sources.list:

```sh
echo "deb http://nginx.org/packages/ubuntu/ lucid nginx" >> /etc/apt/sources.list
```

```sh
echo "deb-src http://nginx.org/packages/ubuntu/ lucid nginx" >> /etc/apt/sources.list
```

Lo instalamos con 

```sh 
sudo apt-get install nginx
```
Ahora haremos una configuración roud-robin así que editamos la ruta

```sh
/etc/nginx/config.d/default.conf 
```
Le indicamos los servidores a los cuales tiene que enviar el tráfico el balanceador como se muestra en la siguiente captura: 
![alt tag](https://github.com/NajMetaich/SWAP16/blob/master/Pr%C3%A1cticas/Pr%C3%A1ctica3/confignginx.PNG?raw=true) 

Más tarde, para comprobar que funciona correctamente haremos peticiones a la IP del balanceador a ver si nos redirige a las mismas máquinas. 

```sh
curl http://192.168.227.131
curl http://192.168.227.131
```
![alt tag](https://github.com/NajMetaich/SWAP16/blob/master/Pr%C3%A1cticas/Pr%C3%A1ctica3/curl.PNG?raw=true)

Editamos el archivo `/etc/nginx/config.d/default.conf` para que una de las máquinas reciba el doble de carga que la otra: 

```sh
upstream apaches {
 server 192.168.227.128 weight=1;
 server 192.168.227.129 weight=2;
} 
```
![alt tag](https://github.com/NajMetaich/SWAP16/blob/master/Pr%C3%A1cticas/Pr%C3%A1ctica3/weight.PNG?raw=true)


Ahora reiniciamos el servicio y hacemos curl para comprobar que funciona correctamente: 
```sh
PS C:\Users\Najlae Metaich> curl http://192.168.227.131


StatusCode        : 200
StatusDescription : OK
Content           : <html>

                    <body>

                    <p>Maquina 2</p>
                    </body>

                    </html>


RawContent        : HTTP/1.1 200 OK
                    Connection: keep-alive
                    Accept-Ranges: bytes
                    Content-Length: 51
                    Content-Type: text/html
                    Date: Sun, 05 Jun 2016 16:41:25 GMT
                    ETag: "33-5348a75392ef0"
                    Last-Modified: Sun, 05 Jun 20...
Forms             : {}
Headers           : {[Connection, keep-alive], [Accept-Ranges, bytes], [Content-Length, 51], [Content-Type,
                    text/html]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 51



PS C:\Users\Najlae Metaich> curl http://192.168.227.131


StatusCode        : 200
StatusDescription : OK
Content           : <html>

                    <body>

                    <p>Maquina 1</p>
                    </body>

                    </html>

RawContent        : HTTP/1.1 200 OK
                    Connection: keep-alive
                    Accept-Ranges: bytes
                    Content-Length: 50
                    Content-Type: text/html
                    Date: Sun, 05 Jun 2016 16:41:29 GMT
                    ETag: "32-5348a6f90e7f3"
                    Last-Modified: Sun, 05 Jun 20...
Forms             : {}
Headers           : {[Connection, keep-alive], [Accept-Ranges, bytes], [Content-Length, 50], [Content-Type,
                    text/html]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 50



PS C:\Users\Najlae Metaich> curl http://192.168.227.131


StatusCode        : 200
StatusDescription : OK
Content           : <html>

                    <body>

                    <p>Maquina 1</p>
                    </body>

                    </html>

RawContent        : HTTP/1.1 200 OK
                    Connection: keep-alive
                    Accept-Ranges: bytes
                    Content-Length: 50
                    Content-Type: text/html
                    Date: Sun, 05 Jun 2016 16:41:32 GMT
                    ETag: "32-5348a6f90e7f3"
                    Last-Modified: Sun, 05 Jun 20...
Forms             : {}
Headers           : {[Connection, keep-alive], [Accept-Ranges, bytes], [Content-Length, 50], [Content-Type,
                    text/html]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 50
```

## Balanceador haproxy

Instalamos haproxy `apt-get install haproxy`. Luego paramos el servicio nginx con service nginx stop. 

Abrimos el el archivo de configuración `haproxy.cfg` y ponemos: 

```sh
global
	daemon
	maxconn 256
defaults
	mode http
	contimeout 4000
	clitimeout 42000
	srvtimeout 43000
frontend http-in
	bind *:80
	default_backend servers
backend servers
	server m1 192.168.227.128:80 maxconn 32
	server m2 192.168.227.129:80 maxconn 32
```
Guardamos, comprobamos la configuración mediante: 

```sh
sudo /usr/sbin/haproxy -f /etc/haproxy/haproxy.cfg 
```
Luego, en la Powershell hacemos un curl como hicimos antes: 

![alt tag](https://github.com/NajMetaich/SWAP16/blob/master/Pr%C3%A1cticas/Pr%C3%A1ctica3/haproxycurl.PNG?raw=true)


Vamos a configurar la ponderación dándole más carga a una que a la otra como hicimos anteriormente: 

```sh
global
    daemon
    maxconn 256

defaults
    mode http
    contimeout 4000
    clitimeout 42000
    srvtimeout 43000

frontend http-in
    bind *:80
    default_backend servers

backend servers
    server m1 192.168.227.128:80 weight 2
    server m2 192.168.227.129:80 weight 1
``` 
```sh
PS C:\Users\Najlae Metaich> curl http://192.168.227.131


StatusCode        : 200
StatusDescription : OK
Content           : <html>

                    <body>

                    <p>Maquina 2</p>
                    </body>

                    </html>


RawContent        : HTTP/1.1 200 OK
                    Accept-Ranges: bytes
                    Content-Length: 51
                    Content-Type: text/html
                    Date: Sun, 05 Jun 2016 17:04:22 GMT
                    ETag: "33-5348a75392ef0"
                    Last-Modified: Sun, 05 Jun 2016 16:29:20 GMT
                    Server:...
Forms             : {}
Headers           : {[Accept-Ranges, bytes], [Content-Length, 51], [Content-Type, text/html], [Date, Sun, 05 Jun 2016
                    17:04:22 GMT]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 51



PS C:\Users\Najlae Metaich> curl http://192.168.227.131


StatusCode        : 200
StatusDescription : OK
Content           : <html>

                    <body>

                    <p>Maquina 1</p>
                    </body>

                    </html>

RawContent        : HTTP/1.1 200 OK
                    Accept-Ranges: bytes
                    Content-Length: 50
                    Content-Type: text/html
                    Date: Sun, 05 Jun 2016 17:04:54 GMT
                    ETag: "32-5348a6f90e7f3"
                    Last-Modified: Sun, 05 Jun 2016 16:27:45 GMT
                    Server:...
Forms             : {}
Headers           : {[Accept-Ranges, bytes], [Content-Length, 50], [Content-Type, text/html], [Date, Sun, 05 Jun 2016
                    17:04:54 GMT]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 50



PS C:\Users\Najlae Metaich> curl http://192.168.227.131


StatusCode        : 200
StatusDescription : OK
Content           : <html>

                    <body>

                    <p>Maquina 1</p>
                    </body>

                    </html>

RawContent        : HTTP/1.1 200 OK
                    Accept-Ranges: bytes
                    Content-Length: 50
                    Content-Type: text/html
                    Date: Sun, 05 Jun 2016 17:04:57 GMT
                    ETag: "32-5348a6f90e7f3"
                    Last-Modified: Sun, 05 Jun 2016 16:27:45 GMT
                    Server:...
Forms             : {}
Headers           : {[Accept-Ranges, bytes], [Content-Length, 50], [Content-Type, text/html], [Date, Sun, 05 Jun 2016
                    17:04:57 GMT]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 50
``` 







