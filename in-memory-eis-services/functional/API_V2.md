# Menu

*Por mejorar desde perspectiva funcional*

- [Menu](#menu)
- [**Operaciones**](#operaciones)
  - [**InMemoryEisCore**](#inmemoryeiscore)
    - [**Set Information**](#set-information)
      - [**Tipo Petición Rest**](#tipo-petición-rest)
      - [**Request Rest**](#request-rest)
      - [**Parámetros**](#parámetros)
      - [**Response Rest**](#response-rest)
    - [**Update Information**](#update-information)
      - [**Tipo Petición Rest**](#tipo-petición-rest-1)
      - [**Request Rest**](#request-rest-1)
      - [**Parámetros**](#parámetros-1)
      - [**Response Rest**](#response-rest-1)
    - [**Delete Information**](#delete-information)
      - [**Tipo Petición Rest**](#tipo-petición-rest-2)
      - [**Request Rest**](#request-rest-2)
      - [**Parámetros**](#parámetros-2)
      - [**Response Rest**](#response-rest-2)
    - [**Get Information XML**](#get-information-xml)
      - [**Tipo Petición Rest**](#tipo-petición-rest-3)
      - [**Request Rest**](#request-rest-3)
      - [**Parámetros**](#parámetros-3)
      - [**Response Rest**](#response-rest-3)
    - [**Get Information JSON**](#get-information-json)
      - [**Tipo Petición Rest**](#tipo-petición-rest-4)
      - [**Request Rest**](#request-rest-4)
      - [**Parámetros**](#parámetros-4)
      - [**Response Rest**](#response-rest-4)

# **Operaciones**

## **InMemoryEisCore**

### **Set Information**
Esta operación permite insertar un registro EIS nuevo. La tabla a continuación muestra todos los parámetros de entrada y salida que tiene la operación, así como la descripción. También define si un campo de petición puede ser opcional o no. El tipo de dato que recibe el servicio se lo presenta en la columna TIPO. 

#### **Tipo Petición Rest**
POST

#### **Request Rest**
```json
[{'idSubscriber':'0969249902'}]
```

#### **Parámetros**
| Parámetro | Descripción |
|:---------:|-----------|
|domainInformation|Nombre del dominio de informacion a filtrar|
|serviceInformation|Nombre del servicio de informacion a filtrar|
|key|Llave hacia donde se asociará el valor|
```
​http://SERVER_IP:SERVER_PORT/inmemoryeis​/v2​/setInformation/{domainInformation}/{serviceInformation}/{key}
```

#### **Response Rest**
```json
{
	"domainInformation":"device",
	"serviceInformation":"0999052343",
	"data":"[{'id': '0999052343','category': 1}]"
}
```

### **Update Information**
Esta operación permite actualizar un registro EIS existente. La tabla a continuación muestra todos los parámetros de entrada y salida que tiene la operación, así como la descripción. También define si un campo de petición puede ser opcional o no. El tipo de dato que recibe el servicio se lo presenta en la columna TIPO. 

#### **Tipo Petición Rest**
PUT
#### **Request Rest**
> ​http://SERVER_IP:SERVER_PORT/inmemoryeis​/v2​/updateInformation/{domainInformation}/{serviceInformation}/{key}

#### **Parámetros**
| Parámetro | Descripción |
|:---------:|-----------|
|domainInformation|Nombre del dominio de informacion a filtrar|
|serviceInformation|Nombre del servicio de informacion a filtrar|
|key|Llave hacia donde se asociará el valor|


```json
[{'idSubscriber':'0969249902'}]
```

#### **Response Rest**
```json
{
	"domainInformation":"device",
	"serviceInformation":"0999052343",
	"data":"[{'id': '0999052343','category': 1}]"
}
```

### **Delete Information**
Esta operación permite eliminar un registro EIS existente. La tabla a continuación muestra todos los parámetros de entrada y salida que tiene la operación, así como la descripción. También define si un campo de petición puede ser opcional o no. El tipo de dato que recibe el servicio se lo presenta en la columna TIPO. 

#### **Tipo Petición Rest**
DELETE

#### **Request Rest**
​http://SERVER_IP:SERVER_PORT/inmemoryeis​/v2​/deleteInformation/{domainInformation}/{serviceInformation}/{key}

#### **Parámetros**
| Parámetro | Descripción |
|:---------:|-----------|
|domainInformation|Nombre del dominio de informacion a filtrar|
|serviceInformation|Nombre del servicio de informacion a filtrar|
|key|Llave hacia donde se eliminará el valor|

```sh
​http://SERVER_IP:SERVER_PORT/inmemoryeis​/v2​/deleteInformation/{domainInformation}/{serviceInformation}/{key}
```

#### **Response Rest**
```json
{
   "status": "OK",
   "message": "Transaction Succesful"
}
```

### **Get Information XML**
Esta operación permite obtener un registro EIS existente. La tabla a continuación muestra todos los parámetros de entrada y salida que tiene la operación, así como la descripción. También define si un campo de petición puede ser opcional o no. El tipo de dato que recibe el servicio se lo presenta en la columna TIPO. El formato es converido en XML

#### **Tipo Petición Rest**
GET

#### **Request Rest**
​http://SERVER_IP:SERVER_PORT/inmemoryeis​/v2​/getInformationJson/{domainInformation}/{serviceInformation}/{key}

#### **Parámetros**
| Parámetro | Descripción |
|:---------:|-----------|
|domainInformation|Nombre del dominio de informacion a filtrar|
|serviceInformation|Nombre del servicio de informacion a filtrar|
|key|Llave hacia donde se eliminará el valor|

```sh
​http://SERVER_IP:SERVER_PORT/inmemoryeis​/v2​/getInformationJson/{domainInformation}/{serviceInformation}/{key}
```

#### **Response Rest**
```json
{
	"status": "OK",
	"message": "Succesful Transaction",
	"data": "<selfserviceregister><access_token>eyJhbGciOiJSUzI1NiIsInR5cCIgOiAiSldUIiwia2lkIiA6ICJWdWttZlg2dHJCRTNDYlNEVDlQMkJ5WE80Unh5bGREV0NEbTZLcHM0TnZzIn0.eyJleHAiOjE2MjY0Njc0MzksImlhdCI6MTYyNjM4MTAzOSwi8yY2q7lVv9LDdQrz531ykL2Ju2l8UegvxYxxaaVBcvyhYT54v3Jn4VMR2_73yFfWAoYh0oOM_XG6X_O9j9IqX2f4A6lg4tTCqHUk_9BOAM1B80dYmr3qPRGvf_yA-miOB4YEsRk7o1KrmVrpvY5zRGwc7a46uSgo2pSQABxVnZVS9HaVW4UjNYWs98VhuQg562W2xSBWrjJDQCPn1RcAwe2yWrkctnaPuha_NE8qJjUp0Ww</access_token></selfserviceregister>"
}
```


### **Get Information JSON**
Esta operación permite obtener un registro EIS existente. La tabla a continuación muestra todos los parámetros de entrada y salida que tiene la operación, así como la descripción. También define si un campo de petición puede ser opcional o no. El tipo de dato que recibe el servicio se lo presenta en la columna TIPO. El formato es converido en JSON

#### **Tipo Petición Rest**
GET

#### **Request Rest**
​http://SERVER_IP:SERVER_PORT/inmemoryeis​/v2​/getInformationJson/{domainInformation}/{serviceInformation}/{key}

#### **Parámetros**
| Parámetro | Descripción |
|:---------:|-----------|
|domainInformation|Nombre del dominio de informacion a filtrar|
|serviceInformation|Nombre del servicio de informacion a filtrar|
|key|Llave hacia donde se eliminará el valor|

```sh
​http://SERVER_IP:SERVER_PORT/inmemoryeis​/v2​/getInformationJson/{domainInformation}/{serviceInformation}/{key}
```

#### **Response Rest**
```json
{
	"status": "OK",
	"message": "Succesful Transaction",
	"data": [{
		"access_token": "eyJhbGciOiJSUzInh5bGREV0N..."
	}]
}
```