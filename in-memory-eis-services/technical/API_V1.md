# **Menu**
- [**Menu**](#menu)
- [**Operaciones**](#operaciones)
  - [**putInformation**](#putinformation)
    - [**Descripcion Put Domain**](#descripcion-put-domain)
    - [**Tipo Petición Rest**](#tipo-petición-rest)
    - [**Estructura Request**](#estructura-request)
    - [**Request Rest**](#request-rest)
    - [**Request Soap**](#request-soap)
    - [**Estructura Response**](#estructura-response)
    - [**Response Rest**](#response-rest)
    - [**Response Soap**](#response-soap)
  - [**modifyDomain**](#modifydomain)
    - [**Descripción Modify Domain**](#descripción-modify-domain)
    - [**Tipo Petición Rest**](#tipo-petición-rest-1)
    - [**Estructura Request**](#estructura-request-1)
    - [**Request Rest**](#request-rest-1)
    - [**Request Soap**](#request-soap-1)
    - [**Estructura Response**](#estructura-response-1)
    - [**Response Rest**](#response-rest-1)
    - [**Response Soap**](#response-soap-1)
  - [**getInformation**](#getinformation)
    - [**Descripcion Get Information**](#descripcion-get-information)
    - [**Tipo Petición Rest**](#tipo-petición-rest-2)
    - [**Estructura Request**](#estructura-request-2)
    - [**Request Rest**](#request-rest-2)
    - [**Request Soap**](#request-soap-2)
    - [**Estructura Response**](#estructura-response-2)
    - [**Response Rest**](#response-rest-2)
    - [**Response Soap**](#response-soap-2)

# **Operaciones**

## **putInformation**

### **Descripcion Put Domain**

Esta operación permite insertar un registro EIS nuevo. La tabla a continuación muestra todos los parámetros de entrada y salida que tiene la operación, así como la descripción. También define si un campo de petición puede ser opcional o no. El tipo de dato que recibe el servicio se lo presenta en la columna TIPO.


### **Tipo Petición Rest**
* POST

### **Estructura Request**
| Parámetro | Tipo | Requerido | Descripción |
|:---------:|:----:|:---------:|-----------|
|DOMAININFORMATION|STRING|SI|Id del dominio a registrar.|
|SERVICEINFORMATION|STRING|SI|Id del dominio a registrar.|
|DATA|STRING|SI|Información a insertar.|


### **Request Rest**
```json
{
	"domainInformation":"device",
	"serviceInformation":"0999052343",
	"data":"[{'id': '0999052343','category': 1}]"
}

```
### **Request Soap**
```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:eis="http://claro.com/InMemoryEIS">
   <soapenv:Header/>
   <soapenv:Body>
      <eis:putInformation>
         <information>
            <domainInformation>subscriber</domainInformation>
            <serviceInformation>0999999999</ serviceInformation>
            <data>[{'id': '0999999999','category': 5}]</data>
         </information>
      </eis:putInformation>
   </soapenv:Body>
</soapenv:Envelope>
```

### **Estructura Response**
| Parámetro | Tipo | Requerido | Descripción |
|:---------:|:----:|:---------:|-----------|
|STATUS |STRING|N/A|Estado de la transacción, los posibles valores son: **OK**, **WARNING**, **ERROR**.|
|MESSAGE |STRING|N/A|Mensaje que indica el resultado de la operación, este puede ser ‘Transaction succesful’ o un texto indicando el mensaje de advertencia o de error en caso de ser así.|
|DATA|STRING|SI|Información a insertada.|

### **Response Rest**
```json
{
   "status": "OK",
   "message": "Transaction Succesful",
   "data": [   {
      "id": "0999052343",
      "category": 1
   }]
}


```

### **Response Soap**
```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
   <soap:Body>
      <ns2:putInformationResponse xmlns:ns2="http://claro.com/InMemoryEIS">
         <return>
            <status>OK</status>
            <message>Transaction Succesful</message>
               <xml><![CDATA[<![CDATA[<subscriber><id>0999999999</id><category>5</category></subscriber>]]]]>><![CDATA[]]></xml>
         </return>
      </ns2: putInformationResponse>
   </soap:Body>
</soap:Envelope>
```



## **modifyDomain**
### **Descripción Modify Domain**
>>>
Esta operación permite actualizar un registro EIS existente. La tabla a continuación muestra todos los parámetros de entrada y salida que tiene la operación, así como la descripción. También define si un campo de petición puede ser opcional o no. El tipo de dato que recibe el servicio se lo presenta en la columna TIPO. 
>>>

### **Tipo Petición Rest**
* POST

### **Estructura Request**
| Parámetro | Tipo | Requerido | Descripción |
|:---------:|:----:|:---------:|-----------|
|DOMAININFORMATION|STRING|SI|Id del dominio a registrar.|
|SERVICEINFORMATION|STRING|SI|Id del dominio a registrar.|
|DATA|STRING|SI|Información a insertar.|

### **Request Rest**
```json
{
	"domainInformation":"device",
	"serviceInformation":"0999052343",
	"data":"[{'id': '0999052343','category': 1}]"
}
```


### **Request Soap**
```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:eis="http://claro.com/InMemoryEIS">
   <soapenv:Header/>
   <soapenv:Body>
      <eis:modifyInformation>
         <information>
            <domainInformation>subscriber</ domainInformation >
            <serviceInformation>0999999999</serviceInformation>
            <data>[{'id': '0999999999','category': 5}]</data>
         </information>
      </eis:update>
   </soapenv:Body>
</soapenv:Envelope>
```

### **Estructura Response**
| Parámetro | Tipo | Requerido | Descripción |
|:---------:|:----:|:---------:|-----------|
|STATUS |STRING|N/A|Estado de la transacción, los posibles valores son: **OK**, **WARNING**, **ERROR**.|
|MESSAGE |STRING|N/A|Mensaje que indica el resultado de la operación, este puede ser ‘Transaction succesful’ o un texto indicando el mensaje de advertencia o de error en caso de ser así.|
|DATA|STRING|SI|Información a insertada.|

### **Response Rest**
```json
{
   "status": "OK",
   "message": "Transaction Succesful",
   "data": [   {
      "id": "0999052343",
      "category": 1
   }]
}
```


### **Response Soap**
```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
   <soap:Body>
      <ns2:updateResponse xmlns:ns2="http://claro.com/EISSOAPWS">
         <return>
            <status>OK</status>
            <message>Transaccion Exitosa</message>
               <xml><![CDATA[<![CDATA[<subscriber><id>0999999999</id><category>5</category></subscriber>]]]]>><![CDATA[]]></xml>
         </return>
      </ns2:updateResponse>
   </soap:Body>
</soap:Envelope>
```



## **getInformation**
### **Descripcion Get Information**
>>>
Esta operación permite buscar un registro EIS según el ServiceInformation y el domainInformation enviados en el request. La tabla a continuación muestra todos los parámetros de entrada y salida que tiene la operación, así como la descripción. También define si un campo de petición puede ser opcional o no. El tipo de dato que recibe el servicio se lo presenta en la columna TIPO. 
>>>


### **Tipo Petición Rest**
* POST

### **Estructura Request**
| Parámetro | Tipo | Requerido | Descripción |
|:---------:|:----:|:---------:|-----------|
|DOMAININFORMATION|STRING|SI|Id del dominio a registrar.|
|SERVICEINFORMATION|STRING|SI|Id del dominio a registrar.|
|DATA|STRING|SI|Información a insertar.|

### **Request Rest**
http://SERVER_IP:SERVER_PORT/eisinmemory/inmemoryeis/getInformationJson/domainInformationd:device/serviceInformation:0999052342


### **Request Soap**
```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:eis="http://claro.com/InMemoryEIS">
   <soapenv:Header/>
   <soapenv:Body>
      <eis:getInformation>
         <domainInformation>subscriber</ domainInformation >
         <serviceInformation>0999999999</serviceInformation>
      </eis:getInformation>
   </soapenv:Body>
</soapenv:Envelope>
```

### **Estructura Response**
| Parámetro | Tipo | Requerido | Descripción |
|:---------:|:----:|:---------:|-----------|
|STATUS |STRING|N/A|Estado de la transacción, los posibles valores son: **OK**, **WARNING**, **ERROR**.|
|MESSAGE |STRING|N/A|Mensaje que indica el resultado de la operación, este puede ser ‘Transaction succesful’ o un texto indicando el mensaje de advertencia o de error en caso de ser así.|
|DATA|STRING|SI|Información a insertada.|

### **Response Rest**
```json
{
   "status": "OK",
   "message": "Transaction Succesful",
   "data":    [
         {
         "id": "‘0999052342'",
         "type": 1
      }
   ]
}
```


### **Response Soap**
```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
   <soap:Body>
      <ns2:getInformationResponse xmlns:ns2="http://claro.com/InMemoryEIS">
         <return>
            <status>OK</status>
            <message>Transaccion Exitosa</message>
            <data><![CDATA[<![CDATA[<subscriber><id>0999999999</id><category>5</category></subscriber>]]]]>><![CDATA[]]></data>
         </return>
      </ns2:getInformationResponse>
   </soap:Body>
</soap:Envelope>
```