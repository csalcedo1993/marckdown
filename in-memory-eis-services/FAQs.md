# FAQ

>**Pregunta:** ¿Qué significa Last Resource?

>**Respuesta:** Representa a la funcionalidad que le permite a In Memory EIS consultar informacion hacia microgateway, el valor en ese campo es el id de requerimiento.

---
>**Pregunta:** ¿En donde puedo desplegar la aplicación?

>**Respuesta:** La aplicación puede ser deplegado en un ambiente dockerizado o en openshift adaptándose a la estructura de Conecel.

---
>**Pregunta:** ¿Donde se almacena la información enviada por el componente?

>**Respuesta:** InMemoryEis utiliza Redis para almacenar la información que es enviada

---
>**Pregunta:** ¿Cuales son las ip de consumo del api?

>**Respuesta:** Las ip de consumo balanceada se aplica en la ip 10.31.32.13

---
>**Pregunta:** ¿Donde encuentro los logs?

>**Respuesta:** Los logs se encuentran en la página 

---
>**Pregunta:** ¿Cómo saber cual es la versión instalada?

>**Respuesta:** La versión instalada se puede ver en el código fuente, en el pom.xml o en el despliegue realizado, cada despliegue debe tener su respectiva versión desplegada.

---
>**Pregunta:** ¿Cómo quitar una instancia de un nodo del nodo?

>**Respuesta:** Las instrucciones en las que puede quitar una o varias instancias de un nodo, así como quitar un servidor del nodo puede verificarse en el [troubleshooting](TROUBLESHOOTING.md#menu).

---
>**Pregunta:** ¿Qué pasa cuando cae un nodo?

>**Respuesta:** Las instrucciones en las que le orientan a los pasos a seguir cuando se cae un nodo puede verificarse en el troublehooting.

---
>**Pregunta:** ¿Cómo reiniciar las aplicaciones en todos los nodos?

>**Respuesta:** Las instrucciones en las que le orientan a reiniciar las aplicaciones en todos los nodos puede verificarse en el troublehooting.

---
>**Pregunta:** ¿Qué tipo de usuario puede ejecutar las acciones?

>**Respuesta:** El usuario de soporte debe de tener los privilegios de acceder a todos los nodos donde se encuentra la aplicación y también acceso a ejecutar comandos de sistema operativo.

---
>**Pregunta:** ¿Cuando se reinicia la aplicacion se carga todo a la memoria?

>**Respuesta:** Cuando la aplicación es iniciada carga toda la información de metadata a la memoria redis, esto sucede en cada nodo

---
>**Pregunta:** Me retorna un Bad Request al realizar una petición, ¿Qué debo realizar?

>**Respuesta:** Para los errores de Bad Request debe validar que la información enviada en le petición cumpla con el contrato establecido.

---
>**Pregunta:** Tengo connection refused al usar el servicio, ¿Qué debo realizar?

>**Respuesta:** Validar la IP y Puerto del servicio que se está invocando, además si el usuario que intenta realizar la petición tiene acceso de red hacia la ip de In Memory Eis.

---
>**Pregunta:** Me retorna 404, ¿Qué debo realizar?

>**Respuesta:** Se debe tomar en cuenta los dos escenarios. Validar el contexto de la aplicación que se está consumiendo y si el Host e IP del servicio que se está consumiendo son los relacionados al componente In Memory Eis 

---
>**Pregunta:** El refresh caché se realiza en cada nodo?

>**Respuesta:** El caché se debe realizar en cada nodo donde se encuentra instalado In Memory Eis.

---
>**Pregunta:** No se ha actualizado los datos que he subido. ¿Qué debo realizar?

>**Respuesta:** Generalmente debe esperar unos minutos cuando se realiza la subida de información a Redis cuando se realiza la carga masiva, esto sucede por que el volume de datos es alto. Para las operaciones insertadas directamente no es necesario esperar. Si la información no se ha subido y no ha dado error verifique la información enviada tal como DomainInformation, ServiceInformation y Key.