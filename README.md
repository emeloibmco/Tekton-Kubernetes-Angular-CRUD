# <h1 align=center> Tekton Kubernetes Angular CRUD

Aplicación Web desarrollada en el Stack MEAN para realizar las operaciones CRUD integrada con la herramienta Tekton para el despliegue e integración automáticos en un cluster de Kubernetes.

Esta demo es un acercamiento inicial que permite crear, leer, actualizar y borrar registros de una base de datos por medio de llamados al API de Express que está conectado con la base de datos MongoDB.

Con Tekton aseguramos el despliegue en un cluster de Kubernetes con la opción de realizar cambios que se vean reflejados en el entorno de producción en el menor tiempo posible con las respectivas pruebas, facilitando el Test-Driven Development (TDD) y las metodologías ágiles como DevOps.

El repositorio se encuentra organizado en dos submodulos, Tekton_Back y Tekton_Front, cada uno con la respectiva parte de la aplicación.

## 📑 Tabla de contenido

1. [Arquitectura](#-arquitectura)
2. [Requisitos](#requisitos)
3. [Despliegue de aplicación de ejemplo](#despliegue-de-aplicación-de-ejemplo)
4. [Hands On!](#hand-hands-on)<br>
   4.1 [Plugins](#instalar-o-actualizar-los-plugins-necesarios-de-ibm-cloud-cli)<br>
   4.2 [Despliegue de la base de datos](#desplegar-la-imagen-de-nuestra-base-de-datos)<br>
   4.3 [Depliegue de la aplicación con Tekton](#-desplegar-nuestra-aplicación-con-tekton)<br>
6. [Referencias y documentación útil](#referencias)
<br />

## 📦 Arquitectura

<p align=center><img src=".github/arch.png"></p>

## Requisitos

- Tener un servicio de **[Kubernetes Cluster](https://cloud.ibm.com/kubernetes/clusters)** disponible en la cuenta IBM Cloud. Para cuentas Lite este servicio está disponible por 30 días.
- :cloud: [IBM Cloud CLI](https://cloud.ibm.com/docs/cli?topic=cloud-cli-getting-started&locale=en)
- :whale: [Docker](https://www.docker.com/products/docker-desktop)
- Contar con un servicio *Continuos Delivery* en la misma región y grupo de recursos del clúster.
- Tener un namespace vacío en el Container Registry.
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/). La version de esta herramienta debe ser compatible con la version de IKS que se desplegó en la cuenta.
- [TypeScript](https://www.typescriptlang.org/#download-links)
- [Angular CLI](https://cli.angular.io/)
- [Node y NPM](https://nodejs.org/en/)
<br />
   
## Despliegue de aplicación de ejemplo
1. Acceda al clúster de Kubernetes y de click en la pestaña ```DevOps```. Posteriormente presione el botón ```Create a toolchain +``` y seleccione la opción ```Build your own toolchain```. Asigne un nombre, e indique la región y grupo de recursos (debe seleccionar los mismo datos del clúster).
   
   <p align=center><img src="https://github.com/emeloibmco/Teckton-Kubernetes-Angular-CRUD/blob/master/Imagenes/1_Crear_Toolchain.gif"></p>
   <br />
   
2. En la pestaña *Overview* del *Toolchain* de click en el botón ```Add tool +```. Allí seleccione la opción ```Git Repos and Issue Tracking```. Complete los campos solicitados de la siguiente manera:
   
   * ```Sever```: deje el valor que sale por defecto.
   * ```Repository Type```: seleccione *Clone*.
   * ```Source Repository URL```: coloque https://github.com/DianaEspitia/teckton-hello-example.
   * ```Repository Owner```: deje el valor que sale por defecto.
   * ```Repository Name```: asigne un nuevo nombre para el repositorio.
   * Habilite únicamente las casillas ```Enable Issues``` y ```Track deployment of code changes```.
  
   Para finalizar de click en el botón ```Create Integration``` y espere a que se complete el proceso.
   
   <p align=center><img src="https://github.com/emeloibmco/Teckton-Kubernetes-Angular-CRUD/blob/master/Imagenes/2_Agregar_Git.gif"></p>
   <br />
   
3. De click nuevamente en el botón ```Add tool +``` y seleccione la opción ```Delivery Pipeline Private Worker```. Complete los campos solicitados de la siguiente manera:
   
   * ```Name```: asigne un nombre exclusivo.
   * ```Service ID API Key```: de click en el botón ```New +``` para generar una nueva API Key. Copiela y tenga en cuenta el nombre del ID del servicio pues necesitará ambos datos más adelante.
   
   Para finalizar de click en el botón ```Create Integration``` y espere a que se complete el proceso.
   
   <p align=center><img src="https://github.com/emeloibmco/Teckton-Kubernetes-Angular-CRUD/blob/master/Imagenes/3_Tekton_worker.gif"></p>
   <br />
   
4. Luego debe configurar el worker. Para ello, de click sobre la integración creada y vaya a la pestaña ```Gettings started```. Allí complete los campos solicitados de la siguiente manera:
   
   * ```Service ID API Key```: coloque el valor de la API Key generada anteriormente.
   * ```Worker name```: asigne un nombre exclusivo.
   * De click en el botón ```Generate```.
   
   Posteriormente, debe registrar el nodo privado con los comandos que aparecen luego de generar el worker pool. Para ello, abra una ventana del *IBM Cloud Shell*, y asegúrese de colocar los siguientes comandos:
   
   ```powershell
   ibmcloud target -r <REGION> -g <GRUPO_RECURSOS>
   ```
   
   ```powershell
   ibmcloud ks cluster config --cluster <cluster_name>
   ```
   
   Y luego, coloque los comandos que aparecen para agregar el nodo privado.
   
   <p align=center><img src="https://github.com/emeloibmco/Teckton-Kubernetes-Angular-CRUD/blob/master/Imagenes/4_Configurar_worker.gif"></p>
   <br />
   
   También puede verificar en la pestaña *Overview* que ya aparece el worker pool.
   
   <p align=center><img src="https://github.com/emeloibmco/Teckton-Kubernetes-Angular-CRUD/blob/master/Imagenes/Worker_pool_ok.PNG"></p>
   <br />
   
5. De click de nuevo en el botón ```Add tool +``` y seleccione la opción ```Delivery Pipeline```. Complete los campos solicitados de la siguiente manera:
   
   * ```Pipeline name```: asigne un nombre exclusivo.
   * ```Pipeline type```: seleccione la opción ```Tekton```.
   
   Para finalizar de click en el botón ```Create Integration``` y espere a que se complete el proceso.
   
   <p align=center><img src="https://github.com/emeloibmco/Teckton-Kubernetes-Angular-CRUD/blob/master/Imagenes/5_Crear_pipeline.gif"></p>
   <br />
   
6. De click sobre el ```Delivery pipeline``` para realizar la respectivas configuraciones. 
   
   ### Pestaña Definitions  
     * ```Repository```: seleccione el respositorio.
     * ```Input type```: seleccione la opción *Branch*.
     * ```Branch```: coloque *main*.
     * ```Path```: seleccione *.tekton*.
     * De click en el botón ```Add```.
     * Espere unos minutos y de click en el botón ```Save```.
     
     <p align=center><img src="https://github.com/emeloibmco/Teckton-Kubernetes-Angular-CRUD/blob/master/Imagenes/6_Definitions.gif"></p>
        
   ### Pestaña Worker
     * ```Worker```: seleccione el worker agregado.
     * De click en el botón ```Save```.
   
     <p align=center><img src="https://github.com/emeloibmco/Teckton-Kubernetes-Angular-CRUD/blob/master/Imagenes/7_Workers.gif"></p>
     
   ### Pestaña Triggers
   **Git Repository**
     * De click en el botón ```Add```.
     * Seleccione la opción ```Git Repository```.
     * ```Trigger name```: asigne un nombre.
     * ```EventListener```: deje el valor inidcado por defecto.
     * ```Worker```: deje el valor inidcado por defecto.
     * ```Repository```: seleccione el repositorio.
     * ```Input type```: seleccione *Branch*.
     * ```Branch```: indique *main*.
     * Habilite la casilla ```When a commit is pushed```.
     * De click en el botón ```Add```.
   
   **Manual**
     * De click en el botón ```Add```.
     * Seleccione la opción ```Manual```.
     * ```Trigger name```: asigne un nombre.
     * ```EventListener```: deje el valor inidcado por defecto.
     * ```Worker```: deje el valor inidcado por defecto.
     * De click en el botón ```Add```.
   
     <p align=center><img src="https://github.com/emeloibmco/Teckton-Kubernetes-Angular-CRUD/blob/master/Imagenes/8_Triggers.gif"></p>
   
   
   ### Pestaña Environment Properties
   De click en el botón ```Add``` y seleccione ```Secure Value```.
      * ```Name```: apikey.
      * ```Value```: coloque el valor de la API Key generado en pasos anteriores.
   
   De click en De click en el botón ```Add``` y seleccione ```Text Value```.
      * ```Name```: cluster y ```Value```: nombre de su clúster.
      * ```Name```: clusterNamespace y ```Value```: namespace del clúster en donde se va a desplegar la aplicación. En este caso *prod*.
      * ```Name```: clusterRegion y ```Value```: región del clúster.
      * ```Name```: registryNamespace y ```Value```: namespace del Container Registry en donde se deplegará la imagen de la aplicación.
      * ```Name```: registryRegion y ```Value```: región del Container Registry.
      * ```Name```: repository y ```Value```: URL del repositorio generado. Si no sabe cual es, en el *toolchain* haga click en *Git* y copie el enlace que abre.
      * ```Name```: revision y ```Value```: *main*.   
   
   <p align=center><img src="https://github.com/emeloibmco/Teckton-Kubernetes-Angular-CRUD/blob/master/Imagenes/variables.PNG"></p>

7. Debe asignar acceso al *toolchain* sobre el Container Registry. Para ello, realice lo siguiente:
   * Haga click en la pestaña ```Manage``` ➡ ```Access (IAM)```.
   * Seleccione la pestaña ```Service IDs```.
   * Seleccione el ID de servicio que corresponde al *toolchain*.
   * De click en la pestaña ```Access policies``` ➡ ```Asign access```.
   * Seleccione la opción ```IAM services```.
   * Busque container registry y asigne todos los permisos.
   * De click en los botones ```Add``` y ```Assign```.
   
   <p align=center><img src="https://github.com/emeloibmco/Teckton-Kubernetes-Angular-CRUD/blob/master/Imagenes/10_permisos_acceso.gif"></p>
   
8. El paso final consiste en correr el pipeline. Para ello, en la pestaña ```PipelineRuns``` de click en el botón ```Run pipeline``` y espere a que se complete el proceso.
   
   <p align=center><img src="https://github.com/emeloibmco/Teckton-Kubernetes-Angular-CRUD/blob/master/Imagenes/11_Run_pipeline.gif"></p>
   
9. Para probar le funcionamiento de la aplicación, abra el dashboard de Kubernetes, seleccione el namespace y en la pestaña ```Services``` haga click en la URL generada.
   
   <p align=center><img src="https://github.com/emeloibmco/Teckton-Kubernetes-Angular-CRUD/blob/master/Imagenes/12_Funcionamiento.gif"></p>   
   
<br />
   
   
   
   
   
   
-----------------
   > NOTA: La sección que se presenta a continuación está pendiente por revisión. Por motivos de actualización, algunos de los comandos en los archivos para el despliegue de la aplicación en pipeline tekton se encuentran obsoltes. En el repositorio https://github.com/DianaEspitia/Team2021-Practicas-Tekton-Back se dejaron algunos cambios actualizados, aún asi se debe continuar con la revisión y los cambios necesarios para completar el despliegue.
-----------------

## :hand: Hands On!

### Instalar o actualizar los plugins necesarios de IBM Cloud CLI.

Reemplazar `install` con `update` si es el caso:

```sh
ibmcloud plugin install kubernetes-service
ibmcloud plugin install container-registry
```

### Desplegar la imagen de nuestra base de datos.

Para este caso vamos a desplegar la base de datos en un pod separado a nuestra aplicación para tener mayor control sobre ella y su aseguramiento, por lo que no usaremos Tekton para esta imagen sino que lo haremos de forma manual. Con este fin, necesitaremos configurar nuestra CLI.

- Iniciar sesión en nuestro terminal de comandos:

  `ibmcloud cr login`

  **Importante**: Tener en cuenta la región que aparece en pantalla, ya que tomará parte de los pasos siguientes.

- Crear espacio de nombres:

  `ibmcloud cr namespace-add <namespace>`

  Este espacio de nombres tambien formará parte de los comandos siguientes. Se puede comprobar el nombre en cualquier momento usando el comando: `ibmcloud cr namespaces`

- Descargar la imagen de MongoDB del registro Docker Hub:<br/>
  Para descargar la imagen oficial de MongoDB, almacenada en el registro de imagenes Docker Hub, utilizar el comando:

  `docker pull mongo:latest`

- Cambiar tag de la imagen para que sea compatible con la region y el espacio de nombre de nuestro IKS:<br/>
  `docker tag mongo:latest <region>/<namespace>/mongo`

- Subir imagen a Container Registery:
  `docker push <region>/<namespace>/mongo`
- Configurar el plugin kubernetes-service:

  `ibmcloud cs cluster config --cluster <nombre_cluster>`

  Si no conocemos el nombre de nuestro cluster podemos utilizar el comando `ibmcloud cs clusters` y seleccionar el que vayamos a utilizar

- Desplegar la imagen en nuestro Cluster:

  `kubectl create deployment mongo --image=<region>/<namespace>/mongo`

- Exponer el Pod creado:
   <h3>Si el cluster esta en infraestructura clasica</h3>

   ```sh
   kubectl expose deployment/mongo --type="NodePort" --port=27017
   ```
   <h3>Si el cluster esta en VPC</h3>
   
   ```sh
   kubectl expose deployment/mongo --type="LoadBalancer" --name=lb-mongo --port=27017 --target-port=27017
   ```
   

   <h1>Para poder enlazar la imagen creada con nuestra aplicación:</h1>
   <h3>Si esta en infraestructura clasica:</h3>
   <p>Obtenemos el puerto y la direccion ip con los siguientes comandos</p>
   <ol>
     <li>Puerto:</li>
      
   ```sh
      kubectl describe service mongo
   ```
      
     <li>Ip:</li>
      
   ```sh
      ibmcloud cs workers --cluster <nombre_cluster>
   ```
      
   </ol>

  Con la IP y el puerto editamos el archivo database.ts ubicado en la carpeta **Tekton_Back/src**, modificando las lineas de conexión con la base de datos, como en la siguiente imagen:

  <p align=center><img src=".github/base_de_datos.png" height="250"></p>
      <h3>Si esta en VPC:</h3>
      <p>Entramos en el panel de control de kubenertes y buscamos en el apartado de <b>services</b> y copiamos el external endpoint </p>
      <p align=center><img src="https://user-images.githubusercontent.com/53380760/158046123-3c81974d-c7ba-4e9d-b39f-375ed11f458b.png" ></p>
      <p>Luego modificamos el archivo database.ts ubicado en la carpeta <b>Tekton_Back/src</b>, modificando las lineas de conexión con la base de datos, como en la siguiente  imagen:</p>
      <p align=center><img src="https://user-images.githubusercontent.com/53380760/158046298-94da3f99-7610-4404-afdc-4973d84b5ffe.png"></p>
     
### <img src=".github/tekton-pipelines.png" height="48"> Desplegar nuestra aplicación con Tekton

Para desplegar nuestra aplicación de Backend, nos dirigimos al repositorio Tekton_Back, que se encuentra enlazado como submodulo, donde podemos encontrar la carpeta .tekton, la carpeta scripts, el archivo deployment.yml y el archivo Dockerfile, recursos que utilizará Tekton para realizar el despliegue.

**Nota**: El proceso para desplegar nuestra aplicación con Tekton es el mismo tanto para Backend como Frontend.

<p align=center><img src=".github/tekton-files.png" height="400"></p>

1. Nos dirigimos a la página de [IBM Cloud ☁️](https://cloud.ibm.com), iniciamos sesión y damos clic a la sección de DevOps en el panel lateral izquierdo, como se ve en la imagen.

<p align=center><img src=".github/devops-section.png" height="450"></p>

2. Procedemos a crear un nuevo toolchain(cadena de herramientas) seleccionando el botón create toolchain.

**Importante**: Revisar que la localización de nuestra toolchain sea la misma a nuestro cluster de Kubernetes

En la pantalla de creación seleccionamos el filtro delivery pipeline-tekton.Luego damos click en **Desarrollar una app de Kubernetes**

<p align=center><img src="https://user-images.githubusercontent.com/53380760/158046561-df7f29ed-32c1-44d9-a8a8-093be665aa2c.png" height="450"></p>
      
      
3. Escogemos el nombre de nuestra toolchain, la region de nuestro cluster, el grupo de recursos, el servidor del repositorio.

   Nos debe redirigir a la siguiente pantalla.
      <p align=center><img src="https://user-images.githubusercontent.com/53380760/158046721-318123c8-d89d-4e4b-ae0c-b13f291707ce.png"></p>

4. Configuramos la integraciones de herramients :

- GitHub:
      Repositorio utilizado`https://github.com/Sebastian1811/Kubernetes-Tekton-Back`

 <p align=center><img src="https://user-images.githubusercontent.com/53380760/158046802-9973d7d0-ee3e-489f-b4df-bbf4fcb41b1e.png"></p>

- Delivery Pipeline:

   Generamos una api key y las casillas faltantes se rellenaran en automatico

 <p align=center><img src="https://user-images.githubusercontent.com/53380760/158046848-d386c54d-ccec-4236-8415-32819c2bca54.png"></p>

Así deberá quedar el Toolchain:

<p align=center><img src="https://user-images.githubusercontent.com/53380760/158046908-ad8ddc6a-03b7-40ef-a43f-181cfbf9d267.png"></p>

Los triggers del toolchain se configuraran de manera automatica.En caso de necesitar configurar una rama diferente del repositorio para el toolchain agregar
en el delivery pipeline una variable de entorno valor de texto cuyo nombre sea **revision** y su valor sea el nombre de la rama. Ejemplo:
      
<p align=center><img src="https://user-images.githubusercontent.com/53380760/158047036-580cd21e-3d83-4963-a484-83862a24376e.png"></p>  
      

Una vez finalizado esto podemos ir a la pestaña PipelineRuns y ejecutar manualmente un nuevo pipelineRun tambien el toolchain se ejecutara cada vez que se cree un commit.

### <p align=center><img src="https://user-images.githubusercontent.com/53380760/158047185-15e07a28-0f01-4f55-ad63-3016c134fde8.png"></p>

<br />
   
## Referencias
* https://www.ibm.com/cloud/architecture/tutorials/develop-kubernetes-app-using-tekton-delivery-pipelines

## Autores

Equipo IBM Cloud Tech Sales Colombia

**Copyright 2020 IBM**
