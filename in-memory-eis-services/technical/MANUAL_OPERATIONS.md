# **InMemoryEIS Operaciones Manuales**
![](https://img.shields.io/badge/version-v2.3.0-green.svg?style=for-the-badge)

# **Features**
- [**InMemoryEIS Operaciones Manuales**](#inmemoryeis-operaciones-manuales)
- [**Features**](#features)
  - [**Permite almacenar, obtener, editar y eliminar información en Memoria**](#permite-almacenar-obtener-editar-y-eliminar-información-en-memoria)
    - [**Escanear patron de Keys**](#escanear-patron-de-keys)
    - [**Consultar data de Redis via GET HTTP.**](#consultar-data-de-redis-via-get-http)
    - [**Exportar patron de Keys.**](#exportar-patron-de-keys)
    - [**Eliminar patron de Keys.**](#eliminar-patron-de-keys)
    - [**Set Redis Keys - Insertar en base Redis Master.**](#set-redis-keys---insertar-en-base-redis-master)


## **Permite almacenar, obtener, editar y eliminar información en Memoria**

El producto inMemoryEIS de claro permite tener servicios de información de consulta agrupadas en dominios para soluciones de negocio. Sus dependencias de tecnología son: (1) Base de datos en memoria REDIS. Las versiones instaladas son dos: BlackList version sin tecnologia de
contenedores. La version Cloud que tiene tecnologia de contenedores

### **Escanear patron de Keys**
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


### **Consultar data de Redis via GET HTTP.**

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


### **Exportar patron de Keys.**
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


### **Eliminar patron de Keys.**
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

### **Set Redis Keys - Insertar en base Redis Master.**

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
