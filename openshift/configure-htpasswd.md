### Configurar Htpasswd como Identity Provider

1. Añadir usuarios al fichero de usuarios. La Opcion -c solo se usa con el primer usuario, para el resto ya sin esa opcion:

        $ htpasswd -c -B -b users.htpasswd user1 MyPassword!
        $ htpasswd -B -b users.htpasswd user2 MyPassword!

2. Crear el secreto para encriptar el fichero:

        $ oc create secret generic htpass-secret --from-file=htpasswd=users.htpasswd -n openshift-config

3. Crear el CR para el identity provider:

        $ vi OAuth_htpassw_CR.yaml
        apiVersion: config.openshift.io/v1
        kind: OAuth
        metadata:
          name: cluster
        spec:
          identityProviders:
          - name: htpasswd_provider
            mappingMethod: claim
            type: HTPasswd
            htpasswd:
              fileData:
                name: htpass-secret

        $ oc apply -f OAuth_htpassw_CR.yaml

4. Una vez que se acceda por primera vez con el usuario aparecera en la lista de usuarios:

        $ [root@masterdns htpasswd]# oc get user
        NAME        UID                                    FULL NAME   IDENTITIES
        twistlock   5d1b0d93-35f2-11ea-a914-0a580a820130               htpasswd_provider:twistlock
        user1       a59db764-35e5-11ea-920a-0a580a830133               htpasswd_provider:user1

        $ [root@masterdns htpasswd]# oc get identity
        NAME                          IDP NAME            IDP USER NAME   USER NAME   USER UID
        htpasswd_provider:twistlock   htpasswd_provider   twistlock       twistlock   5d1b0d93-35f2-11ea-a914-0a580a820130
        htpasswd_provider:user1       htpasswd_provider   user1           user1       a59db764-35e5-11ea-920a-0a580a830133

### Actualizando usuarios


1. Obtener el fichero htpasswd a partir del secreto:

       $ oc get secret htpass-secret -ojsonpath={.data.htpasswd} -n openshift-config | base64 -d > users.htpasswd

2. Añadir o borrar usuarios del fichero users.htpasswd.

      Para añadir un nuevo usuario:

        $ htpasswd -bB users.htpasswd <username> <password>

      Para borrar un usuario existente:

        $ htpasswd -D users.htpasswd <username>


3. Reemplazar el secreto existente con el fichero users.htpasswd actualizado:

        $ oc create secret generic htpass-secret --from-file=htpasswd=users.htpasswd --dry-run -o yaml -n openshift-config | oc replace -f -

4. Si se han borrado usuarios se deben borrar manualmente los recursos de cada uno:
      Borrar el usuario:

        $ oc delete user <username>

      Borrar la identidad del usuario:

        $ oc delete identity my_htpasswd_provider:<username>
