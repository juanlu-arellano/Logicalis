# Instrucciones Laboratorio 4

## CI/CD simple con Openshift

### 1. Crear un Jenkins

  1.1. Definir variable de entorno **GUID**:

    $ export GUID=<iniciales>

  1.2. Crear un proyecto para Jenkins:

    $ oc new-project $GUID-cicd --display-name="My CICD Project"

  1.3. Crear la aplicacion Jenkins:

    $ oc new-app jenkins-ephemeral --param MEMORY_LIMIT=2Gi --param DISABLE_ADMINISTRATIVE_MONITORS=true --param ENABLE_OAUTH=true
    $ oc set resources dc jenkins --limits=cpu=2 --requests=cpu=1,memory=2Gi

  1.4. Comprobar como despliega, cuando el pod del jenkins (en este caso **jenkins-2-2bpfg**) este **Ready** salir del watch con **Ctrl+C**:

    $ watch oc get pod
    Every 2.0s: oc get pod                                                                                                            Tue May 21 10:16:27 2019

    NAME               READY   STATUS      RESTARTS   AGE
    jenkins-2-2bpfg    1/1     Running     0          110s
    jenkins-2-deploy   0/1     Completed   0          119s

  1.5. Obtener la URL de acceso al Jenkins:

    $ oc get route jenkins -o jsonpath='{.spec.host}{"\n"}'

  1.6. Copiar la URL en el navegador para entrar en el Jenkins y aceptar los certificados.

  1.7 Click en **Login with OpenShift**. Esto mostrara la ventana de Login de OpenShift. Introducir las credenciales.

  1.8. Despues de la autenticacion, se mostrara una pantalla preguntando por permitir a Jenkins acceder a los recursos de Openshift. Dejar los valores por defecto y hacer click en **Allow selected permissions**.

  1.9. A continuacion aparecera la pantalla de home de Jenkins.  

### 2. Preparar el entorno de Desarrollo

  2.1. Crear un proyecto para la aplicacion que se va a desplegar en desarrollo:

    $ oc new-project $GUID-tasks-dev --display-name="My Tasks Project DEV"

  2.2. Desplegar la aplicacion para la que crearemos un pipeline en Jenkins. Este comando creara todos los recursos necesarios para la aplicacion, build configuration, imagestream, deployment config, y service:

    $ oc new-app php~https://github.com/lissettegar/openshiftex.git --as-deployment-config=true

  2.3. Comprobar el progreso del build:

    $ oc logs -f openshiftex-1-build

  2.4. Una vez finalize el build comprobar todos los recursos que se han creado en el proyecto:

    $ oc get all

  2.5. A continuacion crear un route para el acceso a la aplicacion:

    $ oc expose svc openshiftex

  2.6. Deshabilitar el trigger automatico para nuestro deploymentconfig:

    $ oc set triggers dc openshiftex --manual

  2.7. Dar los permisos a correctos a la serviceaccount de jenkins para poder hacer build y deploys (esto se hace porque el jenkins y la app estan en protectos diferentes):

    $ oc policy add-role-to-user edit system:serviceaccount:$GUID-cicd:jenkins -n $GUID-tasks-dev

## 3. Crear un pipeline simple

   3.1. Logarse en el jenkins.

   3.2. En la izquierda seleccionar opcion **Nueva Tarea**.

   3.3. Poner un nombre al Pipeline, por ejemplo Task, seleccionar **Pipeline** en el tipo de Job y click en **OK**.

   3.4. En la siguiente pantalla, seccion script, copiar el siguiente pipeline. Cambiar en el pipeline donde pone **guid** por el valor del **GUID** que se este usando (esta definido 3 veces en el pipeline **guid-tasks**):

    node {
      stage('Build Tasks') {
        openshift.withCluster() {
          openshift.withProject("guid-tasks-dev") {
            openshift.selector("bc", "openshiftex").startBuild("--wait=true")
          }
        }
      }
      stage('Tag Image') {
        openshift.withCluster() {
          openshift.withProject("guid-tasks-dev") {
            openshift.tag("openshiftex:latest", "openshiftex:${BUILD_NUMBER}")
          }
        }
      }
      stage('Deploy new image') {
        openshift.withCluster() {
          openshift.withProject("guid-tasks-dev") {
            openshift.selector("dc", "openshiftex").rollout().latest();
          }
        }
      }
    }  

  3.6. Click **Guargar** para guardar el pipeline.

  3.7. En la pagina del Job, seleccionar **Construir ahora**.

  3.8. El progreso del pipeline se puede var de varias formas:

  * Desde la pantalla del Job se ve el progreso de los Stages.
  * Haciendo click en el numero del build (por ejemplo **#1**) y luego opcion **Console Output**.
  * Desde la pantalla del job, hacer click en **Open Ocean Blue**.

  3.9. Una vez termine el pipeline obtener la URL de la aplicacion y comprabar que se accede correctamente:

    $ oc get route openshiftex -n $GUID-tasks-dev -o jsonpath='{.spec.host}{"\n"}'

## 4. Desplegar en Producción

  4.1. Crear un proyecto para la aplicacion que se va a desplegar en desarrollo:

    $ oc new-project $GUID-tasks-prod --display-name="My Tasks Project PROD"

  4.2. Desplegar la aplicacion en el proyecto de producción:

    $ oc new-app php~https://github.com/lissettegar/openshiftex.git --as-deployment-config=true

  4.3. Comprobar el progreso del build:

    $ oc logs -f openshiftex-1-build

  4.4. Una vez finalize el build comprobar todos los recursos que se han creado en el proyecto:

    $ oc get all

  4.5. A continuacion crear un route para el acceso a la aplicacion:

    $ oc expose svc openshiftex

  4.6. Deshabilitar el trigger automatico para nuestro deploymentconfig:

    $ oc set triggers dc openshiftex --manual

  4.7. Dar los permisos a correctos a la serviceaccount de jenkins para poder hacer build y deploys (esto se hace porque el jenkins y la app estan en protectos diferentes):

    $ oc policy add-role-to-user edit system:serviceaccount:$GUID-cicd:jenkins -n $GUID-tasks-prod

## 5. Modificar el pipeline para desplegar tambien en producción:

  5.1. Editar el pipeline de Jenkins para que quede como el siguiente (cambiar todos los **guid** que aparecen):

    node {
      stage('Build Tasks') {
        openshift.withCluster() {
          openshift.withProject("guid-tasks-dev") {
            openshift.selector("bc", "openshiftex").startBuild("--wait=true")
          }
        }
      }
      stage('Tag Image') {
        openshift.withCluster() {
          openshift.withProject("guid-tasks-dev") {
            openshift.tag("openshiftex:latest", "openshiftex:${BUILD_NUMBER}")
          }
        }
      }
      stage('Deploy new image in DEV') {
        openshift.withCluster() {
          openshift.withProject("guid-tasks-dev") {
            openshift.selector("dc", "openshiftex").rollout().latest();
          }
        }
      }
      stage('Promote to PRODUCTION?') {
        input message: "Promote new code?", ok: "Promote"
      }
      stage('Deploy in PROD') {
    	   openshift.withCluster() {
    	     openshift.withProject("guid-tasks-prod") {
           openshift.tag("guid-tasks-dev/openshiftex:latest", "guid-tasks-prod/openshiftex:latest")	  
           openshift.set("image", "dc/openshiftex", "openshiftex=image-registry.openshift-image-registry.svc:5000/guid-tasks-prod/openshiftex:latest")
   		      openshift.selector("dc", "openshiftex").rollout().latest();
          }
        }
      }
    }

  5.2. Click **Guargar** para guardar el pipeline.

  5.3. En la pagina del Job, seleccionar **Construir ahora**.

  5.4. Ver el progreso del pipeline de cualquiera de las siguientes formas:

  * Desde la pantalla del Job se ve el progreso de los Stages.
  * Haciendo click en el numero del build (por ejemplo **#2**) y luego opcion **Console Output**.
  * Desde la pantalla del job, hacer click en **Open Ocean Blue**.

  5.5. Una vez termine el pipeline obtener la URL de la aplicacion y comprabar que se accede correctamente:

    $ oc get route openshiftex -n $GUID-tasks-prod -o jsonpath='{.spec.host}{"\n"}'

## 4. Limpar el entorno

    $ oc delete project $GUID-tasks-dev $GUID-cicd $GUID-tasks-prod
