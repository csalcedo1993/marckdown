# **InMemoryEIS TroubleShooting Version**
![](https://img.shields.io/badge/version-v2.3.0-green.svg?style=for-the-badge)

# Menu
- [**InMemoryEIS TroubleShooting Version**](#inmemoryeis-troubleshooting-version)
- [Menu](#menu)
  - [**Estructura de instalación de Servicios Docker.**](#estructura-de-instalación-de-servicios-docker)
    - [**redis master**](#redis-master)
    - [**redis slave**](#redis-slave)
    - [**inmemroyeis**](#inmemroyeis)
  - [**Restaurar servicio InmemoryEis Docker.**](#restaurar-servicio-inmemoryeis-docker)
  - [**Revisión de servicio redis base de datos clave valor.**](#revisión-de-servicio-redis-base-de-datos-clave-valor)
  - [**Pasos a seguir para subir el servicio.**](#pasos-a-seguir-para-subir-el-servicio)
  - [**Escanear patron de Keys**](#escanear-patron-de-keys)
  - [**Consultar data de Redis via GET HTTP.**](#consultar-data-de-redis-via-get-http)
  - [**Exportar patron de Keys.**](#exportar-patron-de-keys)
  - [**Eliminar patron de Keys.**](#eliminar-patron-de-keys)
  - [**Set Redis Keys - Insertar en base Redis Master.**](#set-redis-keys---insertar-en-base-redis-master)
  - [**Salud de Redis.**](#salud-de-redis)
    - [**Redis Master**](#redis-master-1)
    - [**Redis Replicas**](#redis-replicas)
  - [**HA-Proxy**](#ha-proxy)
  - [**ACL.**](#acl)
    - [**Ejemplo de definición de un ACL.**](#ejemplo-de-definición-de-un-acl)
  - [**Ejemplo de configuración HA-PROXY con RUTEO**](#ejemplo-de-configuración-ha-proxy-con-ruteo)
    - [**Ha-proxy put y modify al servidor master**](#ha-proxy-put-y-modify-al-servidor-master)
  - [**Ha-proxy como servicio**](#ha-proxy-como-servicio)
  - [**Búsqueda de ha-proxy inmemoryeis como servicio con docker**](#búsqueda-de-ha-proxy-inmemoryeis-como-servicio-con-docker)
  - [**Salud del Balanceador (HAProxy)**](#salud-del-balanceador-haproxy)
  - [**Revisión de los nodos configurados**](#revisión-de-los-nodos-configurados)
  - [**Quitar un nodo del balanceo**](#quitar-un-nodo-del-balanceo)
  - [**Salud del Servicio de la Solución y Logs**](#salud-del-servicio-de-la-solución-y-logs)
  - [**Salud de los Jobs**](#salud-de-los-jobs)
  - [**Reiniciar un Job**](#reiniciar-un-job)
  - [**Revisión de los archivos para carga masiva**](#revisión-de-los-archivos-para-carga-masiva)
    

El producto inMemoryEIS de claro permite tener servicios de información de consulta agrupadas
en dominios para soluciones de negocio. Sus dependencias de tecnología son: (1) Base de datos
en memoria REDIS. Las versiones instaladas son dos: BlackList version sin tecnologia de
contenedores. La version Cloud que tiene tecnologia de contenedores

## **Estructura de instalación de Servicios Docker.**

El área de arquitectura maneja una ruta estándar para la instalación de sus servicios. La siguiente ruta representa el workspace o área de trabajo. 

>/dockerappsvol/dockerappdata

Esta ruta contiene una lista de carpetas de proyectos. En cada carpeta se almacena información de cada proyecto.

**Nota:** Algunos proyectos utilizan este mismo directorio para ubicar logs. Sin embargo los nuevos proyectos deberían mover sus logs a un volumen especifico para logs.

>/dockerlogsvol

Lista de proyectos bajo /dockerappsvol/dockerappdata.
### **redis master**
![](imgs/im01.JPG)

En esta carpeta vemos **docker-compose.yml** que es el archivo de despliegue de servicio

```yml
version: '2'
services:
   redis-master:
      image: 'redis:latest'
      ports:
         - '8989:8989:8989:8989:8989:8989:8989:8989:6379'
      environment:
         - REDIS_REPLICATION_MODE=master
         - ALLOW_EMPTY_PASSWORD=yes
      volumes:
         - './data:/data'
```

### **redis slave**
![](imgs/im02.JPG)

En esta carpeta vemos **docker-compose.yml** que es el archivo de despliegue de servicio

```yml
version: '2'
services:
   redis-replica:
      image: 'redis:latest'
      ports:
         - "8989:6379"
      command: redis-server --slaveof 10.31.32.15 8989
      environment:
         - REDIS_REPLICATION_MODE=slave
         - REDIS_MASTER_HOST=10.31.32.15
         - REDIS_MASTER_PORT_NUMBER=8989
      volumes:
         - './data:/data'
```

En este ejemplo vemos que todo lo que pongamos en data, será enviado al contenedor redis
slave.


### **inmemroyeis**
![](imgs/im03.JPG)

En esta carpeta vemos **docker-compose.yml** que es el archivo de despliegue de servicio.

Adicional el archivo **Dockerfile**, que es el archivo que se utiliza para crear o recrear la imagen del proyecto usando el comando: 
```bash 
sudo docker build -t IMAGEN_NAME:VERSION .
```

Dentro de **service**, estan los logs.
Dentro de **bulkInserFiles** estan los archivos con los SET de clave valor de la base redis.
Dentro de **bin**, estan los archivos **app.jar**, **application.properties** y **log4j2.xml**. Todos estos archivos son enviados al contenedor via su volumen configurado en el archivo **dockercompose.yml**

En las imagenes anteriores deben encontrarse los siguientes directorios

```sh
10.31.32.15: /dockerappsvol/dockerappdata/redismaster (ruta de despliegue de
master)
10.31.32.15: /dockerappsvol/dockerappdata/inMemoryEIS (ruta de despliegue de
inmemoryeis)
10.31.32.15: /dockerappsvol/dockerappdata/inMemoryEIS/bin/db
inmemoryeisdb.mv.db/inmemoryeisdb.trace.db (bases de datos H2)
10.31.32.15: /dockerappsvol/dockerappdata/inMemoryEIS/bin
10.31.32.15: /dockerappsvol/dockerappdata/inMemoryEIS/service/log (ruta de logs)
application.properties/app.jar/log4j2.xml (archivos de configuración y jar que
sube el contenedor)
10.31.32.15: /dockerappsvol/dockerappdata/inMemoryEIS/bulkInsertFiles (ruta
master donde se suben los archivos a leer por la aplicación)
```

Similar a esta debe encontrarse la ruta del servidor 14 donde **redismaster** es **redislave**.

```sh
10.31.32.14: /dockerappsvol/dockerappdata/redisslave (ruta de despliegue de
slave)
10.31.32.14: /dockerappsvol/dockerappdata/inMemoryEIS (ruta de despliegue de
inmemoryeis)
10.31.32.14: /dockerappsvol/dockerappdata/inMemoryEIS/bin/db
inmemoryeisdb.mv.db/inmemoryeisdb.trace.db (bases de datos H2)
10.31.32.14: /dockerappsvol/dockerappdata/inMemoryEIS/bin
application.properties/app.jar/log4j2.xml (archivos de configuración y jar que
sube el contenedor)
10.31.32.14: /dockerappsvol/dockerappdata/inMemoryEIS/service/log (ruta de logs)
10.31.32.14: /dockerappsvol/dockerappdata/inMemoryEIS/bulkInsertFiles (ruta
master donde se suben los archivos a leer por la aplicación – no se usa)
```

## **Restaurar servicio InmemoryEis Docker.**

Normalmente este proceso es lo que debemos hacer siempre.
A continuacion revisaremos los pasos que debemos hacer para subir este servicio. 

**Nota:** Este servicio depende de la imagen: **claro/inmemoryeis:1.0.0**, normalmente esta imagen estará en los archivos de despligues cuando levanta el servicio, si esta imagen no existe, deberá ser recreada, con el archivo **Dockerfile** mas la version jar del proyecto

* Si la imagen no existe debe ser recreada a la version **claro/inmemoryeis:1.0.0**. 
 
  Para buscar la imagen ejecute:
  ```bash
  $sudo docker images | grep **claro/inmemoryeis**
  ```

  ```bash
  $sudo docker images | grep claro/inmemoryeis
  claro/inmemoryeis-admin 2.1.0
  4e226ac411c1 15 months ago 174MB
  claro/inmemoryeis-service 2.1.0
  45e864b7ca64 16 months ago 165MB
  claro/inmemoryeis 1.0.0
  0788a81ffe40 2 years ago 150MB
  ```

* Para recrearse la imagen debe estar disponible el archivo Dockerfile mas la carpeta

  >/dockerappsvol/dockerappdata/inMemoryEIS/bin/app.jar


  ```Dockerfile
  FROM openjdk:8-jdk-alpine
  ADD bin/app.jar /app/app.jar
  ADD bin/application.properties /app/application.properties
  ADD bin/log4j2.xml /app/log4j2.xml
  RUN mkdir /log
  RUN mkdir /bulkInsertFiles
  RUN apk --update add redis
  ENV DEFAULT_OPTIONS="-Duser.timezone=America/Guayaquil -
  Djava.net.preferIPv4Stack=true -Djava.security.egd=file:/dev/./urandom"
  ENV JAVA_OPTS=""
  WORKDIR /app
  ```

  Para recrear la imagen.

  ```sh
  $sudo docker build -t claro/inmemoryeis:1.0.0 .
  ```

  En caso de subir la version de la imagen, esta debe hacer referencia al archivo **dockercompose.yml**

  ```yml
  version: '3'
  services:
   inmemoryeis:
      container_name: inmemoryeis
      image: claro/inmemoryeis:1.0.0
      restart: always
      ports:
         - 8888:8888
      logging:
      driver: "none"
      volumes:
         - ./bin:/app
         - ./service/log:/log
         - ./bulkInsertFiles:/bulkInsertFiles
  ```

* Para subir el contenedor debe estar disponible

```
/dockerappsvol/dockerappdata/inMemoryEIS/bin/db/inmemoryeisdb.mv.db y
inmemoryeisdb.trace.db
```

**PASO 1:** Lo primero que debemos hacer es, revisar el estado del servicio buscandolo a través de su puerto conocido como **8888**.
Si el servidor esta caido un simple: **sudo Docker ps | grep 8888** no sirve ya que no mostrará resultados. En este caso lo recomendable es usar la **opcion -a (all)**. 

Muestra el estado de servicios activos e inactivos.
Como vemos en el siguiente ejemplo, el primer GREP no produjo resultado **[11:15:45]**.
Al ejecutar una segunda vez **[11:15:49]** nos indica que el servicio está en estado **Exited (255) About an hour ago**. 

Aquí lo recomendable es subirlo ya sea a través del Id del contenedor o su nombre. En este caso el Id siempre será la primera columna. **(afdcb83e8980)**

```sh
[11:15:45] msacloud-pgdcmsa01:~$sudo docker ps | grep 8888
[11:15:49] msacloud-pgdcmsa01:~$sudo docker ps -a | grep 8888
afdcb83e8980 claro/inmemoryeis:1.0.0 "/bin/sh -c 'java
${…" 14 months ago Exited (255) About an hour ago 0.0.0.0:8888-
>8888/tcp afdcb83e8980
```

Note que: **[11:15:33] msacloud-pgdcmsa01:~$docker ps | grep 8888** produce como resultado el siguiente mensaje tanto para el servidor 10.31.32.14 y 10.32.32.15

```
**Got permission denied while trying to connect to the Docker daemon socket at
unix:///var/run/docker.sock: Get
http://%2Fvar%2Frun%2Fdocker.sock/v1.38/containers/json: dial unix
/var/run/docker.sock: connect: permission denied**
```

**Dará ese mensaje cuando no usamos SUDO**

**PASO 2:** Se debe revisar la ruta **/dockerappsvol/dockerappdata/inMemoryEIS/bin** y checar el archivo **application.properties**. Alli podemos notar el puerto del servicio el cual nos interesa saber si esta levantado.

```
app.jar application.properties backup db log4j2.xml
[11:19:49] msacloud-pgdcmsa01:/dockerappsvol/dockerappdata/inMemoryEIS/bin$cat
application.properties
```

Dara como resultado lo siguiente:

**spring.redis.host: 10.31.32.14** (ip del servidor), para este caso el mismo servidor.
**spring.redis.port: 8989**, para este caso el servicio debe estar levantado en el puerto **8989**.
Si hay problemas al tratar de levantar este servicio. Y si este servicio se encuentra disponible en el nodo 10.31.32.15 bajo el mismo puerto y contiene la replica de redis, es recomendable cambiar spring.redis.host a esa IP. **Recuerde todo cambio de Ip requiere un reinicio de contenedor.**

Dado que si este contenedor está conproblemas del servicio redis, y esta siendo accedido por un servicio haproxy, bajarlo es una opcion válida.


```conf
server.port=8888
spring.application.name=inMemoryEIS
logging.config=log4j2.xml
cxf.path=/soap
spring.redis.host=10.31.32.14
spring.redis.port=8989
inmemoryeis.cache.duration=86400
spring.h2.console.enabled=true
spring.h2.console.path=/h2
spring.h2.console.settings.web-allow-others=true
spring.datasource.url = jdbc:h2:file:./db/inmemoryeisdb
spring.datasource.username = admin
spring.datasource.password = admin
spring.datasource.driverClassName = org.h2.Driver
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
spring.jpa.generate-ddl=true
spring.jpa.hibernate.ddl-auto=none
spring.http.encoding.charset=UTF-8
spring.http.encoding.enabled=true
spring.http.encoding.force=true
```

Vale la pena tambien recalcar: **inmemoryeis.cache.duration=86400**. Esto indica que la memoria solo esta disponible por un dia. Esto es si los refresh de memoria tienen mayor a un dia de duracion la data estará perdida.

**spring.datasource.url=jdbc:h2:file:./db/inmemoryeisdb** , representa la data de base de dato que es cargada a memoria. **Sin esta base un contenedor al subir no contendría información.** Esta base debería ser la misma para todos los nodos.

## **Revisión de servicio redis base de datos clave valor.**

## **Pasos a seguir para subir el servicio.**

En este caso hemos identificado en el archivo application.properties, que el servicio debe estar en el mismo servidor 
**spring.redis.host=10.31.32.14** y debe correr en el puerto *8989* 
**spring.redis.port=8989** entonces, como no sabemo si el servicio esta arriba o abajo es muy importante utilizar la opcion **-a**. (all) de **docker ps -a**

```sh
[11:19:51] msacloud-pgdcmsa01:/dockerappsvol/dockerappdata/inMemoryEIS/bin$sudo
docker ps -a | grep 8989
4882e8dbde5a redis:latest "dockerentrypoint.s…" 18 months ago Exited (255) About an hour ago
0.0.0.0:8989->6379/tcp redisslave_redis-replica_1
```

En la primera columna tenemos el Id del contenedor,y al final el nombredel contenedor
(redisslave_redis-replica_1) y tambien tiene el estado es **Exited (255) About an hour ago**, para este caso procedemos a iniciarlo.

```
[11:20:07] msacloud-pgdcmsa01:/dockerappsvol/dockerappdata/inMemoryEIS/bin$sudo
docker start 4882e8dbde5a
```

Cuando se sube el servicio de memoria, se debe esperar un momento a que la data cargue a la memoria.

**PASO 3**: Y por último chequeo de memoria

Ya que estamos en el nodo 110.31.32.14, el redis que contiene la data es un slave.
**(redisslave_redis-replica_1)**

```sh
$sudo docker ps | grep redisslave_redis-replica_1
```

Finalmente nos dará los mismos datos que ya hemos visto previos.

```sh
$sudo docker ps | grep redisslave_redis-replica_1
4882e8dbde5a redis:latest "dockerentrypoint.s…" 18 months ago Up 10 minutes 0.0.0.0:8989-
>6379/tcp redisslave_redis-replica_1
```

No era necesario hacer el paso anterior si conocemos el nombre del contenedor exacatamente ya que podemos ingresar al contenedor por su id o su nombre:

**sudo docker exec -it 4882e8dbde5a** bash nos permitirá ir al contenedor.

```sh
[11:32:35] msacloud-pgdcmsa01:/dockerappsvol/dockerappdata/inMemoryEIS/bin $sudo docker exec -it 4882e8dbde5a bash
root@4882e8dbde5a:/data#
```

Alternativamente podemos ir directo con el nombre.

>sudo docker exec -it redisslave_redis-replica_1 bash

```sh
[11:35:48] msacloud-pgdcmsa01:/dockerappsvol/dockerappdata/inMemoryEIS/bin $sudo docker exec -it redisslave_redis-replica_1 bash
root@4882e8dbde5a:/data#
```


## **Escanear patron de Keys**
**Nota**: En el servidor master (**10.32.33.15**) se utiliza: docker ps | grep ab-redis-master
Una vez que estamos dentro del servidor de redis, podemos escanear un patron que exista en memoria.
**redis-cli --scan --pattern 'eis/Subscription/InfoOffer/*'** y obtendremos una lista. Para terminar el proceso podemos presionar **CTRL + C** y para salir del contenedor ponemos EXIT.


```sh
root@4882e8dbde5a:/data# redis-cli --scan --pattern
'eis/Subscription/InfoOffer/*'
eis/Subscription/InfoOffer/100011202
eis/Subscription/InfoOffer/74622
eis/Subscription/InfoOffer/100039639
eis/Subscription/InfoOffer/100040095
eis/Subscription/InfoOffer/100035408
eis/Subscription/InfoOffer/100009675
eis/Subscription/InfoOffer/100037733
eis/Subscription/InfoOffer/100033204
eis/Subscription/InfoOffer/100037519
eis/Subscription/InfoOffer/100033188
eis/Subscription/InfoOffer/90000
eis/Subscription/InfoOffer/100030346
eis/Subscription/InfoOffer/100004141
eis/Subscription/InfoOffer/100029905
```


## **Consultar data de Redis via GET HTTP.**

Finalmente podemos tomar cualquier ID anterior y probar la URL del GET de data.
Si tomamos como ejemplo el **68961**.
Los valores sombreados deben ser reemplazados 
**Balanceador.**
> http://10.31.32.13:8888/inmemoryeis/getInformationJson/domainInformation:Subscription/serviceInformation:InfoSubscriber/key:593939001011

En este caso podemos probar el nodo directamente si tenemos alguna configuración hecha.

> http://10.31.32.14:8888/inmemoryeis/getInformationJson/domainInformation:Subscription/
serviceInformation:InfoSubscriber/key:593939001011

```json
{
   "status":"OK",
   "message":"Succesful Transaction",
   "data":[
      {
            "firstName":"ROSA VIRGINIA",
         "lastName":"PEREZ",
         "paymentType":"PPA",
         "planName":"AMIGO_CHIP",
         "serviceNumber":"593939001011",
         "subscriberStatus":"ACTIVE"
      }
   ]
}

```


## **Exportar patron de Keys.**
```sh
[11:35:48] msacloud-pgdcmsa01:/dockerappsvol/dockerappdata/inMemoryEIS/bin$sudo
docker exec -it redisslave_redis-replica_1 bash
root@4882e8dbde5a:/data#
```

De la misma forma que podemos escanear, podemos exportar

```sh
redis-cli --scan --pattern 'eis/Subscription/InfoOffer/*' > file.txt
```

Para este caso file.txt contendrá una lista de patrones.

En el servidor master (10.31.32.15), la ruta donde se podra recuperar este archivo es la siguiente:

**VOLUMEN:** /dockerappsvol/dockerappdata/redismaster/data
En el servidor master (10.31.32.14), la ruta donde se podra recuperar este archivo es la siguiente:
**VOLUMEN:** /dockerappsvol/dockerappdata/redisslave/data


## **Eliminar patron de Keys.**
```sh
[11:35:48] msacloud-pgdcmsa01:/dockerappsvol/dockerappdata/inMemoryEIS/bin$sudo
docker exec -it redisslave_redis-replica_1 bash
root@4882e8dbde5a:/data#
```

Podemos borrar datos, pero este se debe hacer en el servidor master

```sh
redis-cli KEYS "eis/DOMAIN/SERVICE/*" | xargs redis-cli DEL
```

Tambien se puede borrar archivos tomados desde un file.

En el servidor master (10.31.32.15), la ruta donde se podra ubicar este archivo es la siguiente:

**VOLUMEN:** /dockerappsvol/dockerappdata/redismaster/data

```sh
redis-cli < test_delete.txt
```

donde **test_delete.txt**

```sh
DEL eis/Subscription/InfoSubscriber/593939001001
DEL eis/Subscription/InfoSubscriber/593939001002
```




## **Set Redis Keys - Insertar en base Redis Master.**

En este caso, debemos ubicarnos en el servidor master. 10.31.32.15

```sh
$docker ps | grep redismaster (nos dará el id)
$docker exec -it fc7ea5a23ee1 bash
root@4882e8dbde5a:/data#
```

**VOLUMEN:** /dockerappsvol/dockerappdata/redismaster/data

```sh
redis-cli < create.txt
```

donde **create.txt**

```sh
SET eis/domain/service/1206110569 "[{'idSubscriber':'0969249902'}]"
SET eis/domain/service/1206110569 "[{'idSubscriber':'0969249902'}]"
SET eis/domain/service/1206110569 "[{'idSubscriber':'0969249902'}]"
```

El proceso de crear este configuraciones de DOMINIO Y SERVICIO previsamente se lo debe hacer via el API de InMemoryEis.

Sin embargo se puede insertar directamente un registro en la base redis.


```sh
redis-cli SET eis/domain/service/1206110569 "[{'idSubscriber':'0969249902'}]"
```






## **Salud de Redis.**

La instalación del Redis utilizado para esta solución se trata de una instalación tipo Replicación, lo cual implica que existe una instancia de Redis con un rol master a la cual podrían estar suscritas múltiples instancias de Redis siendo éstas las réplicas.

### **Redis Master**

La instancia master de Redis se encuentra en el servidor 10.31.32.15 de la cloud. Una vez conectado mediante SSH a este servidor debes navegar al directorio en donde se encuentra la instalación de este Redis.


```sh
cd /dockerappsvol/dockerappdata/redismaster/
```

Dentro del directorio ejecutaremos el siguiente comando el cual nos permitirá conocer si los contenedores que conforman parte de la instalación de Redis se encuentran en un estado UP

```sh
docker-compose ps
```
![](imgs/im04.JPG)

En caso de estar detenido puede ejecutar en modo background.

```sh
docker-compose up -d
```


### **Redis Replicas**

Luego de comprobar el estado de la instancia master de Redis, comprobaremos el estado de las replicas a través del siguiente comando:


```sh
sudo docker-compose restart redismaster_redis-master_1
```

```sh
Sudo docker exec -it redismaster_redis-master_1 redis-cli info replication
```

Resultado esperado:

![](imgs/im05.JPG)

Como resultado del comando tenemos información de las replicas activas. Al momento existe una replica en el servidor 10.31.32.14

En caso de caída de servicio

De no aparecer ninguna replica para el master de Redis, será necesario revisar el status de la instalación en el servidor 10.31.32.14. Al igual que en el servidor 10.31.32.15 debemos ingresar al directorio de la instalación en el servidor que revisaremos (10.31.32.14)


```sh
cd /dockerappsvol/dockerappdata/redisslave/
```

Posteriormente ejecutaremos el siguiente comando para revisar el estado del contenedor instalado:


```sh
sudo docker-compose ps
```

![](imgs/im06.JPG)

Si el resultado muestra un estado UP quiere decir que todo está correcto, de no ser así, es posible que la composición esta caída, o que simplemente sea necesario un reinicio del servicio:

**Iniciar composición:**

```sh
sudo docker-compose start
```

**Reiniciar servicio:**

```sh
sudo docker-compose restart redisslave_redis-replica_1
```

## **HA-Proxy**

En el servidor 10.31.32.13 se encuentra instalado el servicio HA-Proxy.

Para revisar la versión instalada ejecute.

```sh
[09:20:54] msacloud-pgdcmsaweb01:~$haproxy -version
HA-Proxy version 1.5.18 2016/05/10
Copyright 2000-2016 Willy Tarreau <willy@haproxy.org>
```

Para mayor información de configuración de haproxy es importante revisar la documentación que corresponde a la versión instalada para eso revise las siguientes urls:

* https://cbonte.github.io/haproxy-dconv/configuration-1.5.html (1.5.18)
* https://cbonte.github.io/haproxy-dconv/
* https://cbonte.github.io/haproxy-dconv/1.5/configuration.html (1.5.19)

Estructura de instalacion haproxy.

Al igual que las estructura del servidor master y esclavos en el servidor 10.31.32.13 se mantiene una structura similar.

> /dockappsvol/dockerappdata/haproxy/cfgs

![](imgs/im07.JPG)

Dentro de esta estructura se encuentra el archivo **docker-compose.yml** que da detalle del servicio y el archivo de configuración **haproxy.cfg**.

## **ACL.**

**Extracto de información.**

Haproxy es capaz de extraer datos de un flujo de requerimientos o respuestas, desde la información del cliente o del servidor, de tablas, información de ambiente, etc.

La acción de extraer dichos datos se denomina buscar una muestra. Una vez recuperado, Estas muestras se pueden utilizar para diversos fines, como la clave de una base de datos clave valor, pero los usos más comunes consisten en compararlos con constantes de datos predefinidas llamados patrones.

### **Ejemplo de definición de un ACL.**

**Extracto de información.**

>>>
In order to define a test, the "acl" keyword is used. The syntax is : acl <aclname> <criterion> [flags] [operator] [<value>] ...
La información fue tomada de la siguiente ruta:
>>>

https://cbonte.github.io/haproxy-dconv/configuration-1.5.html (1.5.18)

## **Ejemplo de configuración HA-PROXY con RUTEO**

**Extracto de información.**

Este es un ejemplo tomado de la documentación de ha-proxy el cual explica dos formas de escuchar un puerto y redireccionarlo a otro servidor.

```conf
# Simple configuration for an HTTP proxy listening on port 80 on all
# interfaces and forwarding requests to a single backend "servers" with a
# single server "server1" listening on 127.0.0.1:8000
global
   daemon
   maxconn 256

defaults
   mode http
   timeout connect 5000ms
   timeout client 50000ms
   timeout server 50000ms

frontend http-in
   bind *:80
   default_backend servers

backend servers
   server server1 127.0.0.1:8000 maxconn 32

# The same configuration defined with a single listen block. Shorter but
# less expressive, especially in HTTP mode.
global
   daemon
   maxconn 256

defaults
   mode http
   timeout connect 5000ms
   timeout client 50000ms
   timeout server 50000ms

listen http-in
   bind *:80
   server server1 127.0.0.1:8000 maxconn 32

```

### **Ha-proxy put y modify al servidor master**

La configuración siguiente se realizó para que cuando el requerimiento sea enviado a través de un SOAP y se utilice ya sea PUT o MODIFY solo se lo haga en el servidor 15.

La expresión regular **inm:modifyInformation**|**inm:putInformation** indica que si el requerimiento tiene una de esas dos etiquetas (O definido por |) se redireccione al servidor 15.

Nota: también existe el método REST que no se ha configurado hasta ahora. Esto es que cuando se usa el método REST para PUT o MODIFY la información, debe usarse directo la IP del MASTER.
(10.131.32.15)

```conf
frontend inmemoryeis-routing-frontend
   bind *:8888
   option http-buffer-request
   acl redirect_inmemoryeisput req.body -m reg <inm:modifyInformation>|<inm:putInformation>
   use_backend inmemoryeisput_backend if redirect_inmemoryeisput
   default_backend inmemoryeis-backend
```

De acuerdo al ejemplo anterior, se escucha la petición por el puerto 8888 y se analiza el requerimiento con un ACL.

```conf
acl redirect_inmemoryeisput req.body -m reg <inm:modifyInformation>|
<inm:putInformation>
```

**nombre**: redirect_inmemoryeisput
**mensaje a analizar**: -m
**expresión regex**: inm:modifyInformation|inm:putInformation
con la opción: **option http-buffer-request** (Permite analizar el request)

## **Ha-proxy como servicio**

Para revisar la configuración de servicio de HAPROXY para inMemoryEis se lo debe hacer en el
servidor **10.31.32.13**, ruta:

> /dockappsvol/dockerappdata/haproxy/cfgs/inMemoryEIS/

```sh
cd /dockappsvol/dockerappdata/haproxy/cfgs/inMemoryEIS/
[10:30:10] msacloudpgdcmsaweb01:/dockappsvol/dockerappdata/haproxy/cfgs/inMemoryEIS$cat dockercompose.yml
version: '3'
services:
   haproxy-inmemoryeis:
   image: haproxy:alpine
   ports:
      - "8888:8888"
   volumes:
      -
   /dockappsvol/dockerappdata/haproxy/cfgs/inMemoryEIS:/usr/local/etc/haproxy:ro
```

Esto indica que se está utilizando una imagen **haproxy:alpine** *image: haproxy:alpine* y el puerto de este servicio es el 8888. (**ports**)

Este servicio tomará la configuración hecha en:

**/dockappsvol/dockerappdata/haproxy/cfgs/inMemoryEIS** y será copiada al contenedor
internamente en **/usr/local/etc/haproxy:ro** (READ ONLY)

Este servicio nunca debería estar abajo, y en caso de estar abajo debe ser subido inmediatamente.

Subir en modo background

```sh
cd /dockappsvol/dockerappdata/haproxy/cfgs/inMemoryEIS/

docker-compose up -d
```

Reiniciar el servicio en caso de aplicarse

```sh
cd /dockappsvol/dockerappdata/haproxy/cfgs/inMemoryEIS/

docker-compose restart
```

Bajar el servicio en caso de necesitarse

```sh
cd /dockappsvol/dockerappdata/haproxy/cfgs/inMemoryEIS/

docker-compose down
```

Parar el servicio en caso de necesitarse

```sh
cd /dockappsvol/dockerappdata/haproxy/cfgs/inMemoryEIS/

docker-compose stop haproxy-inmemoryeis
```

Recuerde, puede hacer una busqueda del servicio con:

```sh
docker ps-a | grep haproxy-inmemoryeis
```

Para subir el servicio, puede hacerlo con el nombre o el id de la busqueda anterior. (**inmemoryeis_haproxy-inmemoryeis_1**). Recuerde que si se desea iniciar un contenedor a traves de su ID, bastará las primeras tres letras, siempre no sea ambiguo con otro contenedor.

```sh
docker start haproxy-inmemoryeis
```

## **Búsqueda de ha-proxy inmemoryeis como servicio con docker**

En ocasiones puede darse el caso que no conozcamos donde se encuentran las configuraciones de cierto servicio, pero sabemos que puerto es. En nuestro caso, sabemos que el puerto expuesto es el 8888 y ese nos sirve como dato de entrada en la búsqueda.

```sh
[10:34:05] msacloudpgdcmsaweb01:/dockappsvol/dockerappdata/haproxy/cfgs/inMemoryEIS$docker ps |
grep 8888
8396f23d7d3b haproxy:alpine "/docker-entrypoint.…" 6
months ago Up 5 weeks 0.0.0.0:8888->8888/tcp
inmemoryeis_haproxy-inmemoryeis_1
```

En este ejemplo podemos ver el ID del contenedor como su nombre.

Podemos inspeccionarlo para tener mayor detalle.

```conf
[10:46:34] msacloudpgdcmsaweb01:/dockappsvol/dockerappdata/haproxy/cfgs/inMemoryEIS$docker inspect
8396f23d7d3b
[
   {
      "Id":
      "8396f23d7d3bcee5cdea1eddd59ca789781b8ce952f9040c9caa536938df862d",
      "Created": "2020-11-30T14:30:21.009608606Z",
      "Path": "/docker-entrypoint.sh",
      "Args": [
         "haproxy",
         "-f",
         "/usr/local/etc/haproxy/haproxy.cfg"
      ],
      "State": {
         "Status": "running",
         "Running": true,
         "Paused": false,
         "Restarting": false,
         "OOMKilled": false,
         "Dead": false,
         "Pid": 103176,
         "ExitCode": 0,
         "Error": "",
         "StartedAt": "2021-04-26T15:02:17.977536839Z",
         "FinishedAt": "2021-04-26T15:01:52.758810273Z"
      },
      "Image":
      "sha256:b90ac5a28bd390ba1e210eedd7908832357ad77aeb977d1289ac09b797bb6ded",
      "ResolvConfPath":
      "/var/lib/docker/containers/8396f23d7d3bcee5cdea1eddd59ca789781b8ce952f9040c9caa
      536938df862d/resolv.conf",
      "HostnamePath":
      "/var/lib/docker/containers/8396f23d7d3bcee5cdea1eddd59ca789781b8ce952f9040c9caa
      536938df862d/hostname",
      "HostsPath":
      "/var/lib/docker/containers/8396f23d7d3bcee5cdea1eddd59ca789781b8ce952f9040c9caa
      536938df862d/hosts",
      "LogPath":
      "/var/lib/docker/containers/8396f23d7d3bcee5cdea1eddd59ca789781b8ce952f9040c9caa
      536938df862d/8396f23d7d3bcee5cdea1eddd59ca789781b8ce952f9040c9caa536938df862djson.log",
      "Name": "/inmemoryeis_haproxy-inmemoryeis_1",
      "RestartCount": 0,
      "Driver": "devicemapper",
      "Platform": "linux",
      "MountLabel": "",
      "ProcessLabel": "",
      "AppArmorProfile": "",
      "ExecIDs": null,
      "HostConfig": {
         "Binds": [
            "/dockappsvol/dockerappdata/haproxy/cfgs/inMemoryEIS:/usr/local/etc/haproxy:ro"
         ],
         "ContainerIDFile": "",
         "LogConfig": {
            "Type": "json-file",
            "Config": {}
         },
         "NetworkMode": "inmemoryeis_default",
         "PortBindings": {
            "8888/tcp": [
               {
                  "HostIp": "",
                  "HostPort": "8888"
               }
            ]
         },
         "RestartPolicy": {
            "Name": "",
            "MaximumRetryCount": 0
         },
         "AutoRemove": false,
         "VolumeDriver": "",
         "VolumesFrom": [],
         "CapAdd": null,
         "CapDrop": null,
         "Dns": null,
         "DnsOptions": null,
         "DnsSearch": null,
         "ExtraHosts": null,
         "GroupAdd": null,
         "IpcMode": "shareable",
         "Cgroup": "",
         "Links": null,
         "OomScoreAdj": 0,
         "PidMode": "",
         "Privileged": false,
         "PublishAllPorts": false,
         "ReadonlyRootfs": false,
         "SecurityOpt": null,
         "UTSMode": "",
         "UsernsMode": "",
         "ShmSize": 67108864,
         "Runtime": "runc",
         "ConsoleSize": [
            0,
            0
         ],
         "Isolation": "",
         "CpuShares": 0,
         "Memory": 0,
         "NanoCpus": 0,
         "CgroupParent": "",
         "BlkioWeight": 0,
         "BlkioWeightDevice": null,
         "BlkioDeviceReadBps": null,
         "BlkioDeviceWriteBps": null,
         "BlkioDeviceReadIOps": null,
         "BlkioDeviceWriteIOps": null,
         "CpuPeriod": 0,
         "CpuQuota": 0,
         "CpuRealtimePeriod": 0,
         "CpuRealtimeRuntime": 0,
         "CpusetCpus": "",
         "CpusetMems": "",
         "Devices": null,
         "DeviceCgroupRules": null,
         "DiskQuota": 0,
         "KernelMemory": 0,
         "MemoryReservation": 0,
         "MemorySwap": 0,
         "MemorySwappiness": null,
         "OomKillDisable": false,
         "PidsLimit": 0,
         "Ulimits": null,
         "CpuCount": 0,
         "CpuPercent": 0,
         "IOMaximumIOps": 0,
         "IOMaximumBandwidth": 0
      },
      "GraphDriver": {
         "Data": {
            "DeviceId": "432",
            "DeviceName": "docker-253:10-16831996-
            c3f7387eea2a52d8eef6f011c3f82148794c8cb999383a5c76a86fc9e1fe8db3",
            "DeviceSize": "10737418240"
         },
         "Name": "devicemapper"
      },
      "Mounts": [
         {
            "Type": "bind",
            "Source": "/dockappsvol/dockerappdata/haproxy/cfgs/inMemoryEIS",
            "Destination": "/usr/local/etc/haproxy",
            "Mode": "ro",
            "RW": false,
            "Propagation": "rprivate"
         }
      ],
      "Config": {
         "Hostname": "8396f23d7d3b",
         "Domainname": "",
         "User": "",
         "AttachStdin": false,
         "AttachStdout": false,
         "AttachStderr": false,
         "ExposedPorts": {
            "8888/tcp": {}
         },
         "Tty": false,
         "OpenStdin": false,
         "StdinOnce": false,
         "Env": [
            "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
            "HAPROXY_MAJOR=1.8",
            "HAPROXY_VERSION=1.8.13",
            "HAPROXY_SHA256=2bf5dafbb5f1530c0e67ab63666565de948591f8e0ee2a1d3c84c45e738220f
            1"
         ],
         "Cmd": [
            "haproxy",
            "-f",
            "/usr/local/etc/haproxy/haproxy.cfg"
         ],
         "ArgsEscaped": true,
         "Image": "haproxy:alpine",
         "Volumes": {
            "/usr/local/etc/haproxy": {}
         },
         "WorkingDir": "",
         "Entrypoint": [
            "/docker-entrypoint.sh"
         ],
         "OnBuild": null,
         "Labels": {
            "com.docker.compose.config-hash":
            "dba9ba8b242b54f9f8b97c6519faaeb1cb0997a7a18e5b379a656bad7c932268",
            "com.docker.compose.container-number": "1",
            "com.docker.compose.oneoff": "False",
            "com.docker.compose.project": "inmemoryeis",
            "com.docker.compose.service": "haproxy-inmemoryeis",
            "com.docker.compose.version": "1.16.1"
         }
      },
      "NetworkSettings": {
         "Bridge": "",
         "SandboxID":
         "3144b716f85fdd563fd9d65233f72b51bd1460843b962dae9c1240fd6360af77",
         "HairpinMode": false,
         "LinkLocalIPv6Address": "",
         "LinkLocalIPv6PrefixLen": 0,
         "Ports": {
            "8888/tcp": [
               {
                  "HostIp": "0.0.0.0",
                  "HostPort": "8888"
               }
            ]
         },
         "SandboxKey": "/var/run/docker/netns/3144b716f85f",
         "SecondaryIPAddresses": null,
         "SecondaryIPv6Addresses": null,
         "EndpointID": "",
         "Gateway": "",
         "GlobalIPv6Address": "",
         "GlobalIPv6PrefixLen": 0,
         "IPAddress": "",
         "IPPrefixLen": 0,
         "IPv6Gateway": "",
         "MacAddress": "",
         "Networks": {
            "inmemoryeis_default": {
               "IPAMConfig": null,
               "Links": null,
               "Aliases": [
                  "8396f23d7d3b",
                  "haproxy-inmemoryeis"
               ],
               "NetworkID":
               "440c4675b050c00d0104e16684e01261261cb6a3a15431bc75ca5675e4a8c217",
               "EndpointID":
               "d84df47e6bdb9ae5893568f12ee02c4c6dc079a90c966a845fa221f29ce34b18",
               "Gateway": "172.21.0.1",
               "IPAddress": "172.21.0.2",
               "IPPrefixLen": 16,
               "IPv6Gateway": "",
               "GlobalIPv6Address": "",
               "GlobalIPv6PrefixLen": 0,
               "MacAddress": "02:42:ac:15:00:02",
               "DriverOpts": null
            }
         }
      }
   }
]

```

## **Salud del Balanceador (HAProxy)**

Ingresar al servidor 10.31.32.13 a través de SSH. Para revisar la salud del balanceador es necesario acudir a la siguiente ruta:

```sh
cd /dockappsvol/dockerappdata/haproxy/cfgs/inMemoryEIS/
```

Posteriormente ejecutamos este comando para ver el estado de la composición:

```sh
docker-compose ps
```

Esperando este resultado

```sh
[15:46:05] msacloudpgdcmsaweb01:/dockappsvol/dockerappdata/haproxy/cfgs/inMemoryEIS$docker-compose
ps
Name Command State
Ports
---------------------------------------------------------------------------------
------------------
inmemoryeis_haproxy-inmemoryeis_1 /docker-entrypoint.sh hapr ... Up
0.0.0.0:8888->8888/tcp
```

Si el estado del servicio es UP quiere decir que está corriendo de forma correcta. Caso contrario será necesario un inicio de la composición con el siguiente comando:

```sh
docker-compose start
```

## **Revisión de los nodos configurados**

Para la revisión del balanceador es necesario conocer que nodos están siendo balanceados. Para ello primero acudimos al directorio en donde se encuentra la configuración del balanceador:

```sh
cd /inMemoryEIS/
```

Posteriormente mostramos el contenido del archivo de configuración de HAProxy:

```sh
cat haproxy.cfg
```

![](imgs/im08.JPG)

En dicha configuración existen dos backends:
El uno posee los nodos a los cuales balancear cuando se tratan de operaciones de lectura en InMemoryEIS (10.31.32.14, 10.31.32.15):

![](imgs/im09.JPG)

y el otro backend posee el redireccionamiento a la única instancia de InMemoryEIS que conecta con Redis Master para escritura (10.31.32.15)

La lógica de enrutamiento está definida en el frontend configurado para este HaProxy:

![](imgs/im10.JPG)

Agregar un nuevo nodo al balanceo
Cuando se necesite agregar un nodo al balanceador primero debemos identificar en que backend lo registraremos, si se trata de una instancia que posee conexión directa al nodo master de Redis debe registrarse en ambos backend, mientras que si se trata de un nodo que no posee conexión directa al nodo master de Redis sino mas bien a una réplica, esta debe ser registrada solo en el backend **inmemoryeis-backend**

Luego de identificar el backend en el que será registrado, procedemos a modificar el archivo de configuración; primero acudimos al directorio que posee dicho archivo:

```sh
cd /dockappsvol/dockerappdata/haproxy/cfgs/inMemoryEIS/
```

Luego abrimos el archivo con cualquier editor de texto para entornos Linux, por ejemplo:

```sh
vi haproxy.cfg
```

Independientemente del backend en el que se registre, agregar un nuevo nodo consiste en lo siguiente:

```conf
backend inmemoryeis-backend
   balance roundrobin
   server msahost01 10.31.32.14:8888 check port 8888
   server msahost02 10.31.32.15:8888 check port 8888
   server <host_name> <ip>:<port> check port <port>
```

* <host_name>: Definir un nombre a la instancia que agregará
* : Especificar la ip de la instancia
* : Especificar el puerto de la instancia


Luego de modificar el archivo de configuración es necesario validar dicha modificación, esto lo hacemos a través del siguiente comando:

```sh
docker exec -i inmemoryeis_haproxy-inmemoryeis_1 haproxy -c -f
/usr/local/etc/haproxy/haproxy.cfg
```

Si la configuración es válida, el servicio HAProxy responderá con el siguiente texto:

Luego procedemos a realizar un reinicio del servicio en caliente con el siguiente comando **(Este reinicio no compromete la disponibilidad del servicio)**

```sh
docker kill -s HUP inmemoryeis_haproxy-inmemoryeis_1
```

## **Quitar un nodo del balanceo**

Al igual que lo especificado anteriormente, quitar un nodo del balanceo solo consiste en modificar el archivo de configuración del HAProxy.

Luego de identificar el backend en el que se eliminará un nodo procedemos a modificar el archivo de configuración; primero acudimos al directorio que posee dicho archivo:

```sh
cd /dockappsvol/dockerappdata/haproxy/cfgs/inMemoryEIS/
```

Luego abrimos el archivo con cualquier editor de texto para entornos Linux, por ejemplo:

```sh
vi haproxy.cfg
```

Independientemente del backend en el que se registre, eliminar un nodo consiste en eliminar la línea de registro o comentarla a través de un (#) tal como se muestra a continación:

```conf
backend inmemoryeis-backend
   balance roundrobin
   server msahost01 10.31.32.14:8888 check port 8888
   #server msahost02 10.31.32.15:8888 check port 8888
```

Luego de modificar el archivo de configuración es necesario validar dicha modificación, esto lo hacemos a través del siguiente comando:

```sh
docker exec -i inmemoryeis_haproxy-inmemoryeis_1 haproxy -c -f
/usr/local/etc/haproxy/haproxy.cfg
```

Si la configuración es válida, el servicio HAProxy responderá con el siguiente texto:

Luego procedemos a realizar un reinicio del servicio en caliente con el siguiente comando **(Este reinicio no compromete la disponibilidad del servicio)**


```sh
docker kill -s HUP inmemoryeis_haproxy-inmemoryeis_1
```

## **Salud del Servicio de la Solución y Logs**

Esta revisión del servicio es aplicable para los dos servidores de la cloud (10.31.32.14, 10.31.32.15)

**Salud del contenedor**

Primero debemos ir al directorio en donde se encuentra la instalación de la solución:

```sh
cd /dockerappsvol/dockerappdata/inMemoryEIS/
```

Para ver el estado de la solución debemos ejecutar el siguiente comando:

```sh
docker-compose ps
```

Esperando el siguiente resultado:

![](imgs/im11.JPG)

Si el resultado muestra un estado UP para el servicio quiere decir que el contenedor esta arriba.

De no ser así será necesario un inicio de la composición.

```sh
docker-compose start
```

Si el resultado muestra un estado UP quiere decir que todo está correcto, de no ser así, es posible que la composición esta caída, o que simplemente sea necesario un reinicio del servicio:

**Iniciar composición:**

```sh
sudo docker-compose start
```

**Reiniciar servicio:**

```sh
sudo docker-compose restart inmemoryeis
```

**Salud del Servicio**

Luego de comprobar que el contenedor se encuentra en un correcto estado, procedemos con la revisión del servicio; para ello vamos a revisar el estado de cada uno de los nodos haciendo uso de una de las operaciones del servicio. En una consola de Linux ejecutamos el siguiente comando:

```sh
curl -XGET http://10.31.32.14:8888/inmemoryeisadmin/getSystemInformation
```

El resultado a pesar de mostrar mucha información, por el momento solo deberá importarle el status que retorne, siendo OK el estado que determina el correcto funcionamiento del servicio

![](imgs/im12.JPG)

**Reinicio de la solución**

Ante una respuesta inesperada o simplemente ante la necesidad de reinicio del artefacto, este debe realizarse ejecutando de la siguiente forma:

Primero debemos ir al directorio en donde se encuentra la instalación de la solución:

```sh
cd /dockerappsvol/dockerappdata/inMemoryEIS/
```

Y posteriormente procedemos a reiniciar el servicio:

```sh
sudo docker-compose restart inmemoryeis
```

**Revisión de logs**

Para la revisión de los logs debemos primero acudir al directorio en donde se encuentran

```sh
cd /dockerappsvol/dockerappdata/inMemoryEIS/service/log
```

Dentro de este directorio existen dos tipos de logs con sus respectivas rotaciones, el del servicio atendiendo las peticiones del usuario y el log de las cargas masivas.

Para ver directamente el flujo del log, puede hacerlo ejecutando el siguiente comando:

```sh
tail -f inMemoryEis.log
```

Dentro del **log del servicio** encontrará el siguiente formato

![](imgs/im13.JPG)

Del cual **[Status=OK]** indica el resultado de la transacción, siendo **OK** el estado para transacción exitosa y **ERROR** para transacción con errores.

**El log de las cargas masivas** solo lo encontrará en el servidor en el que se ejecutan (10.31.32.15).Dicho log tiene el siguiente formato:

![](imgs/im14.JPG)

En donde:

* **[OK=922801]**: Indica el número de registros cargados exitosamente
* **[ERROR=0]**: Indica el número de registros cargados con error
* **[TOTAL=922801]**: Indica el número total de registros cargados

## **Salud de los Jobs**

Los jobs son los procesos encargados de ejecutar el ingreso masivo de registros en Redis. Para esta tarea solo una de las instancias de InMemoryEIS debe tener en ejecución estos Jobs. Dicha configuración actualmente solo se encuentra en el servidor 10.31.32.15 ya que es la única instancia que se conecta con la instancia máster de Redis.

Para consultar el estado de los Jobs, debe ejecutar el siguiente comando:

```sh
curl -XGET http://10.31.32.15:8888/inmemoryeisadmin/getSystemInformation
```

El resultado de la ejecución muestra información de todos los Jobs configurados

![](imgs/im15.JPG)

Cada uno de los registros posee información acerca del estado y programaciones de los Jobs, aquí el detalle.

```json
{
"domainInformation":"Subscription",
"serviceInformation":"InfoSubscriber",
"jobs":[
   {
      "name":"Subscription.InfoSubscriber",
      "status":"RUNNING",
      "triggers":1,
      "cronExpression":"5 30 8 * * ?",
      "lastExecution":"21/03/2021 08:30:05.000-0500",
      "nextExecution":"22/03/2021 08:30:05.000-0500"
   }
]
}
```

- **domainInformation**: Dominio de información del archivo de carga masiva a ejecutarse
- **serviceInformation**: Servicio de información del archivo de carga masiva a ejecutarse
- **jobs**: Configuración del Job
- **jobs.name**: Nombre del Job
- **jobs.status**: Estado del Job, si es RUNNING quiere decir que se encuentra corriendo
- **job.cronExpression**: Expresión crontab para ejecución periódica del Job
- **job.lastExecution**: Fecha y hora de la última vez que se ejecutó el Job
- **job.nextExecution**: Fecha y hora programada para la próxima ejecución el Job

Los jobs pueden ser administrados a través del api que ofrece InMemoryEIS. Dicha API permite detener, iniciar y/o reiniciar un Job.

**Detener un Job**
Para detener un job se debe ejecutar el siguiente comando:
curl -XGET http://10.31.32.15:8888/taskschedule/stopTaskSchedule/domainInformation:
{domainInformation}/serviceInformation:{serviceInformation}
En donde:
- **{domainInformation}**: Dominio de información del Job a detenerse
- **{serviceInformation}**: Servicio de información del Job a detenerse


**Iniciar un Job**
Para iniciar un job se debe ejecutar el siguiente comando:
curl -XGET http://10.31.32.15:8888/taskschedule/startTaskSchedule/domainInformation:
{domainInformation}/serviceInformation:{serviceInformation}
En donde:
- **{domainInformation}**: Dominio de información del Job a iniciarse
- **{serviceInformation}**: Servicio de información del Job a iniciarse

## **Reiniciar un Job**

Para reiniciar un job se debe ejecutar el siguiente comando:
curl -XGET http://10.31.32.15:8888/taskschedule/reloadTaskSchedule/domainInformation:
{domainInformation}/serviceInformation:{serviceInformation}
En donde:
- **{domainInformation}:** Dominio de información del Job a iniciarse
- **{serviceInformation}**: Servicio de información del Job a iniciarse

## **Revisión de los archivos para carga masiva**

Para revisar los archivos utilizados en las cargas masivas puede que primero deba conocer los servicios de información configurados en la solución; para ello vamos a ejecutar el siguiente comando:

```sh
curl -XGET http://10.31.32.15:8888/inmemoryeisadmin
```

El cual devolverá todos los servicios de información que existen configurados:
![](imgs/im16.JPG)

Cada una de las configuraciones existentes poseen estos datos:

```json
{
   "domainInformation":"Subscription",
   "serviceInformation":"InfoSubscriber",
   "description":"Informacion del Subscriptor",
   "pathMassInsertFile":"/bulkInsertFiles/datos_clientes_Hw.txt",
   "cronExpression":"5 30 8 * * ?",
   "status":"A"
}
```

En donde:
- **domainInformation**: Indica el dominio de la información
- **serviceInformation**: Indica el servicio de información
- **description**: Detalla una descripción de lo que trata dicha configuración
- **pathMassInsertFile**: Indica la ruta en la que se encuentra el archivo dentro del contenedor
- **cronExpression**: Indica la expresión crontab para la ejecución periódica del job
- **status**: Indica el estado de la configuración

Luego de conocer la ruta del archivo que queremos revisar, debemos ir al siguiente directorio:

```sh
cd /dockerappsvol/dockerappdata/inMemoryEIS/bulkInsertFiles
```

Una vez dentro de este directorio podemos ver todos los archivos utilizados en las cargas masivas para cada servicio de información

```sh
ll
```

Cada uno de estos archivos deben cumplir con un formato específico. Si abrimos uno de estos podemos observar que el formato es el siguiente:

```sh
SET eis/{domainInformation}/{serviceInformation}/{key} "[ { 'offerId': '100042506' } ]"
```

**SET eis/**: Parte de la sintaxis requerida
**{domainInformation}**: Dominio de información al que corresponde el registro
**{serviceInformation}**: Servicio de información al que corresponde el registro
**{key}**: Clave única entre el servicio de información al que corresponde el registro
**"[ { 'offerId': '100042506' } ]"**: Contenido del registro, este debe ser un elemento JSON 
- La forma correcta de este JSON no es usar **APOSTROFES** sino String escapanda. Esto es que cuando un valor trae un UN ' el SET es invalido, por lo tanto esta es la forma correcta para evitar inconvenientes. **"[ { 'offerId': 'Manuel's house' } ]"** -> es invalido por el posesivo.

Forma correcta es la siguiente:
```sh
SET eis/Subscription/InfoSubscriber/593939001011 "[{\"firstName\":\"ROSA
VIRGINIA\",\"lastName\":\"PEREZ\",\"paymentType\":\"PPA\",\"planName\":\
"AMIGO_CHIP\",\"serviceNumber\":\"593939001011\",\"subscriberStatus\":\"
ACTIVE\"}]"
```

Normalmente estos valores se forman con un Query de Base de Datos.

```sql
select /*+ rule +*/ 'SET eis/Subscription/InfoSubscriber/593'||subs.service_number|| ' "' || REGEXP_REPLACE('[{"firstName":"'||REGEXP_REPLACE(to_char(indv.first_name), '([\|"])', '\\\1', 1, 0)|| '","lastName":"'||REGEXP_REPLACE(to_char(indv.last_name), '([\|"])', '\\\1', 1, 0)|| '","paymentType":"'||case when subs.payment_type=0 then 'PPA' when subs.payment_type=1 and subs.brand=2 then 'AUT' when subs.payment_type=1 and subs.brand=4 then 'TAR' else 'NA' end|| '","planName":"'||nvl(replace(REGEXP_SUBSTR(to_char(offer.offering_name),'("es_LA":")((\\"|[^"])*)'),'"es_LA":"',''),REGEXP_REPLACE(to_char(offer.offering_name),'([\|"])', '\\\1', 1, 0))|| '","serviceNumber":"'||'593'||subs.service_number|| '","subscriberStatus":"'||case when subs.status=2 then 'ACTIVE' when subs.status=4 then 'SUSPENDED' when subs.status=3 then 'BARRED' when subs.status=1 then 'INITIAL' else 'NA' end||'"}]','([\|"])', '\\\1', 1, 0) || '"'
from dwh_Adm.inf_subscriber_merge subs
inner join XXOSBPRO_ODI_COLECTOR.pm_offering offer on
offer.offering_id=subs.offering_id
inner join XXOSBPRO_ODI_COLECTOR.inf_customer cust on
cust.cust_id=subs.owner_party_role_id
inner join XXOSBPRO_ODI_COLECTOR.inf_individual indv on
indv.indv_id=cust.party_id
where (subs.status in (2,3,4) or subs.status=1 and subs.ex_field17 is not null)
```

Esto produce como resultados:

```sh
SET eis/Subscription/InfoSubscriber/593939001001 "[{\"firstName\":\"KRONOS LABORATORIOS\",\"lastName\":\"C. LTDA.\",\"paymentType\":\"PPA\",\"planName\":\"AMIGO_CHIP\",\"serviceNumber\":\"593939001001\",\"subscriberStatus\":\"ACTIVE\"}]"
SET eis/Subscription/InfoSubscriber/593939001011 "[{\"firstName\":\"ROSA VIRGINIA\",\"lastName\":\"PEREZ\",\"paymentType\":\"PPA\",\"planName\":\"AMIGO_CHIP\",\"serviceNumber\":\"593939001011\",\"subscriberStatus\":\"ACTIVE\"}]"
SET eis/Subscription/InfoSubscriber/593939001017 "[{\"firstName\":\"ROSA CELINA\",\"lastName\":\"JARA\",\"paymentType\":\"PPA\",\"planName\":\"AMIGO_CHIP\",\"serviceNumber\":\"593939001017\",\"subscriberStatus\":\"ACTIVE\"}]"
SET eis/Subscription/InfoSubscriber/593939001028 "[{\"firstName\":\"CARLOS\",\"lastName\":\"MEDRANO\",\"paymentType\":\"PPA\",\"planName\":\"AMIGO_CHIP\",\"serviceNumber\":\"593939001028\",\"subscriberStatus\":
\"ACTIVE\"}]"
SET eis/Subscription/InfoSubscriber/593939001035 "[{\"firstName\":\"NEY RUBID\",\"lastName\":\"VALDIVIESO\",\"paymentType\":\"PPA\",\"planName\":\"AMIGO _CHIP\",\"serviceNumber\":\"593939001035\",\"subscriberStatus\":\"ACTIVE\"}]"
SET eis/Subscription/InfoSubscriber/593939001038 "[{\"firstName\":\"CRUZ CULQUI LUIS ALFONSO\",\"lastName\":\"DE\",\"paymentType\":\"PPA\",\"planName\":\"AMIGO_KIT\" ,\"serviceNumber\":\"593939001038\",\"subscriberStatus\":\"ACTIVE\"}]"
SET eis/Subscription/InfoSubscriber/593939001055 "[{\"firstName\":\"DELINA LEONILA\",\"lastName\":\"CALLE\",\"paymentType\":\"PPA\",\"planName\":\"AMIGO_CHIP\",\"serviceNumber\":\"593939001055\",\"subscriberStatus\":\"ACTIVE\"}]"
SET eis/Subscription/InfoSubscriber/593939001059 "[\"firstName\":\"CARMEN PILAR\",\"lastName\":\"PAREDES\",\"paymentType\":\"PPA\",\"planName\":\"PLAN AMIGO CHIP DATOS\",\"serviceNumber\":\"593939001059\",\"subscriberStatus\":\"ACTIVE\"}]"
SET eis/Subscription/InfoSubscriber/593939001089 "[{\"firstName\":\"CATHERINE ESTEFANIA\",\"lastName\":\"CASILLAS\",\"paymentType\":\"PPA\",\"planName\":\"AMI GO_CHIP\",\"serviceNumber\":\"593939001089\",\"subscriberStatus\":\"ACTIVE\"}]"
SET eis/Subscription/InfoSubscriber/593939001102 "[\"firstName\":\"NUVIA DEL ROCIO\",\"lastName\":\"VEGA\",\"paymentType\":\"PPA\",\"planName\":\"AMIGO_KIT\" ,\"serviceNumber\":\"593939001102\",\"subscriberStatus\":\"ACTIVE\"}]"
```