### Uso de Roles predefinidos

1. Logarse en el cluster con un token del usuario kubeadmin:

		oc login --token=hZrSGu0uf0APzxbbGsesDO2IcAhueIy88IXjKDF6xvQ --server=https://api.openshift45.ocp.local:6443
		Login successful.
		You don't have any projects. You can try to create a new project, by running
			oc new-project <projectname>

2. Para ver la lista de todos los clusterroles disponibles:

		oc get clusterrole

		NAME
		admin
		basic-user
		cluster-admin
		...
		<output omitted>
		...

3. Use el comando 'oc describe' para ver que rules estan definidas para un role en particular:

	   oc describe clusterrole/edit

	Puede ver en el resultado anterior que, por ejemplo, los usuarios con este role pueden crear y eliminar recursos como pods, configmaps, deploymentconfigs, imagestream, routes y services, pero no pueden hacer nada con los proyectos, aparte de verlos.

	Por otro lado, si hacemos un describe del cluster role view:

	   oc describe clusterrole/view

	se puede ver que las únicas acciones permitidas en los recursos son obtener, listar y observar, lo que lo convierte en una opción perfecta si, por ejemplo, desea otorgar a un equipo de desarrollo la capacidad para ver los recursos de la aplicación en producción, pero no para modificar ninguno de ellos ni crear nuevos recursos.

4. Logarse con el primer usuario:

		oc login https://api.openshift45.ocp.local:6443 -u user01 -p 1234
		Login successful.

		You don't have any projects. You can try to create a new project, by running

			oc new-project <projectname>



5. Crear un nuevo proyecto

		oc new-project user01-project

6. Logarse como el segundo usuario y comprobar si se tiene acceso al proyecto del primer usuario:

		oc login https://api.openshift45.ocp.local:6443 -u user02 -p 1234

		oc project user01-project
		error: You are not a member of project "user01-project".
		You are not a member of any projects. You can request a project to be created with the 'new-project' command.

7. A continuación daremos permisos al usuario 2 para que tenga privilegios de "edit" en el proyecto del usuario 1:

		oc login https://api.openshift45.ocp.local:6443 -u user01
		oc adm policy add-role-to-user edit user02

8. Comprobar los rolebinding existentes en el proyecto:

		oc get rolebinding

9. Ver los detalles de todos los `rolebinding` del proyecto:

		oc describe rolebinding <nombre>

10. Logarse nuevamente como user02 y comprobar que ahora se tiene acceso al proyecto del user01:

		oc login https://api.openshift45.ocp.local:6443 -u user02
		Logged into "https://api.openshift45.ocp.local:6443" as "user02" using existing credentials.

		You have one project on this server: "user01-project"

		Using project "user01-project".

11. Comprobar que el único proyecto que puede ver el user02 es el `user01-project`:

		oc get project
		NAME             DISPLAY NAME   STATUS
		user01-project                  Active


### Creando Roles Personalizados

Si los roles predefinidos no son suficientes, siempre puede crear roles personalizados con las reglas específicas que necesita.

1. Creemos un role personalizado que se pueda usar en lugar del role de edición para crear y obtener pods:

		oc login --token=hZrSGu0uf0APzxbbGsesDO2IcAhueIy88IXjKDF6xvQ --server=https://api.openshift45.ocp.local:6443
		export GUID=lgp
		oc create clusterrole custom-role-${GUID} --verb=get,list,watch --resource=namespace,project
		clusterrole.rbac.authorization.k8s.io/custom-role-lgp created

	Note que hemos iniciado sesión como cluster-admin para crear un `Clusterrole`.

2. Ver los detalles del nuevo `Clusterrole`:

		oc describe clusterrole custom-role-${GUID}
		Name:         custom-role-lgp
		Labels:       <none>
		Annotations:  <none>
		PolicyRule:
		  Resources                      Non-Resource URLs  Resource Names  Verbs
		  ---------                      -----------------  --------------  -----
		  namespaces                     []                 []              [get list watch]
		  projects.project.openshift.io  []                 []              [get list watch]

3. Añadir el nuevo clusterrole al user02

		oc adm policy add-cluster-role-to-user custom-role-${GUID} user02
		clusterrole.rbac.authorization.k8s.io/custom-role-lgp added: "user02"

		oc get clusterrolebinding |grep $GUID
		custom-role-lgp                                                                   32s

		oc describe clusterrolebinding custom-role-${GUID}
		Name:         custom-role-lgp
		Labels:       <none>
		Annotations:  <none>
		Role:
		  Kind:  ClusterRole
		  Name:  custom-role-lgp
		Subjects:
		  Kind  Name    Namespace
		  ----  ----    ---------
		  User  user02  

4. Comprobar cuantos proyectos puede ver ahora el user02:

		oc login https://api.openshift45.ocp.local:6443 -u uer02
		Logged into "https://api.openshift45.ocp.local:6443" as "user02" using existing credentials.

		You have one project on this server: "user01-project"

		Using project "user01-project".

		oc get project


5. Limpiar el entorno:

		oc login --token=hZrSGu0uf0APzxbbGsesDO2IcAhueIy88IXjKDF6xvQ --server=https://api.openshift45.ocp.local:6443
		oc delete clusterrolebinding custom-role-${GUID}
		oc delete project user01-project		
