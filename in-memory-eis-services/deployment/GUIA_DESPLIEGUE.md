# Menu

# **Instrucciones de descarga**
[Versiones] (./CHANGELOG)
## **Descarga por primera vez**
`git clone -b RAMA_CREADA http://10.31.32.18:8180/utility-services/in-memory-eis-services`

## **Proyecto ya existente**
`git pull -b RAMA_CREADA http://10.31.32.18:8180/utility-services/in-memory-eis-services`

## **TAG**
Por definir


### **Generar nueva version**
Para generar una nueva version de este artefacto deben tenerse en cuenta los siguientes requisitos:
* Cambiar la versión del artefacto en el pom.xml - tag **version**. Aplica para artefactos JAVA.
* Cambiar el package.json - atributo **version** (no aplica)
* Cambiar la versión del jar en el Dockerfile (ruta **./deploy/docker**), debe ser igual a la puesta en el **pom** o en el **package.json**. Aplica para artefactos JAVA y NPM.
* Cambiar la versión en la imagen en el objeto yaml DeploymentConfig (ruta **./deploy/k8s**) - atributo **spec.template.spec.containers.image**.. Aplica para despliegues Openshift.

### **Configuracion Inicial**
#### **Requisitos**
##### **Docker**
* **docker** ya instalado
* **docker-compose** ya instalado
* Imagen de java "**openjdk:8-jre-slim**" ya instalada o acceso a internet a docker hub para la descarga.
* El usuario de sistema operativo que ejecute la tarea debe tener el acceso para poder crear directorios en caso de necesitarlo.
* Se instalarán los artefactos en las rutas estándares de claro para artefactos docker.
##### **Openshift**
* **oc** ya instalado
* Conocimientos de despliegue y disponibilidad de infraestructura y administracion
* Acceso a plataforma de openshift tanto de red como autenticacion
* Poder generar imágenes de docker

### **Dependencias**
* Debe estar iniciado y funcional la base de datos no relacional **mongodb** en el host configurado en la aplicacion
* Debe estar disponible el puerto **8889** o cambiarse en la debida configuración.
* Debe de estar accesible la base de datos en memoria de Redis
* En caso de **Openshift** debe estar creado el proyecto creado donde estará el artefacto
* En caso de **Openshift** el host debe estar definido

### **Estructura de despliegue**
Para un correcto despliegue de los artefactos se debe tener en cuenta los siguientes lineamientos:
* Toda la informacion de despliegue debe encontrarse dentro del directorio '**deploy**' en la raíz de este repositorio.
* Dentro de la carpeta deploy deben de existir dos directorios '**docker**' y '**k8s**'
* Para openshift es necesario que las imágenes de docker generadas existan en un docker registry que tenga acceso openshift.
* Para openshift es necesario que exista una propiedad en el pom llamada **openshiftProjectName** que contendrá el nombre del proyecto de openshift.

#### Openshift
Dentro del directorio de openshift el cual se denomina '**k8s**' deben estar los siguientes archivos:
* deploymentConfig.yaml
  * Contiene informacion sobre el despliegue, tales como replicas, memoria ram, cpu, ConfigMap. hace conjuncion con el nombre de **persistentVolumeClaim.yaml**
* persistentVolumeClaim.yaml
  * Contiene informacion de volumen, estan relacionada a los logs. Este volumen debe ser lectura y escritura, tener en cuenta que muchos pods pueden escribir en este mismo volumen.
* route.yaml
  * Contiene informacion de la url donde se desplegara el artefacto, en openshift, esta url debe existir y tener el permiso necesario para usar host personalizados, hace conjuncion con el nombre de **service.yaml**
* service.yaml
  * Contiene informacion del servicio desplegado, hace conjuncion con el nombre de **deploymentConfig.yaml**
  
#### **Docker**
Dentro del directorio de docker el cual se denomina '**docker**' deben estar los siguientes archivos:
* Dockerfile
  * Contiene informacion sobre la generacion de imagen de docker
* docker-compose.yml
  * Contiene informacion sobre el despliegue de los artefactos usando **docker-compose**
### **Build**
_Si el proyecto necesita algunos pasos adicionales para que el desarrollador compile después de algunos cambios de código se deben indicar aqui_
Cambios en archivo application.properties

## **Despliegue** 
Seguir las instrucciones de despliegue para la tecnología a aplicar. Docker u Openshift

## **Construido con**
* [Docker](https://www.docker.com/) - Capa adicional de abstracción y automatización de virtualización de aplicaciones en múltiples sistemas operativos
* [Spring Boot](https://spring.io/projects/spring-boot) - Framework para el desarrollo de aplicaciones y contenedor de inversión de control, de código abierto para la plataforma Java
* [Java](https://www.java.com) - La plataforma Java es el nombre de un entorno o plataforma de computación originaria de Sun Microsystems, capaz de ejecutar aplicaciones desarrolladas usando el lenguaje de programación Java u otros lenguajes que compilen a bytecode y un conjunto de herramientas de desarrollo
* [Openshift](https://www.openshift.com/) - OpenShift, formalmente llamado Openshift Container Platform (OCP), es un producto de computación en la nube de plataforma como servicio de Red Hat.
