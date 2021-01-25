### Laboratorio 18

### Desplegar gitlab runner en Openshift

1. Desplegar el operador GitLab Runner y configurarlo:

       $ oc new-project lgp-git
       $ vi lgp-eo.yaml
       apiVersion: operators.coreos.com/v1
       kind: OperatorGroup
       metadata:
         name: lgp-git
         namespace: lgp-git
       spec:
         targetNamespaces:
         - lgp-git

       $ oc create -f lgp-eo.yaml

       $ vi gitlab-runner-operator-suscription.yaml
       apiVersion: operators.coreos.com/v1alpha1
       kind: Subscription
       metadata:
         name: gitlab-runner-operator
         namespace: lgp-git
       spec:
         channel: beta
         name: gitlab-runner-operator
         source: certified-operators
         sourceNamespace: openshift-marketplace

        $ oc create -f  gitlab-runner-operator-suscription.yaml

2. Crear un secreto con el token necesario para confirar los runners para GitLab:

       $ oc create secret generic runner-secrets --from-literal runner-registration-token=hiYP3YJKu5fi5yJZb-Jx -n lgp-git

3. Crear el runner:

       $ vi runner-gitlab.yaml
       apiVersion: apps.gitlab.com/v1beta2
       kind: Runner
       metadata:
         name: gitlab-runner
         namespace: lgp-git
       spec:
         gitlabUrl: http://10.20.97.80/
         token: runner-secrets
         tags: openshift, test

       $ oc create -f runner-gitlab.yaml

4. Comprobar que el runner aparece en GitLab y configurar para que corra en proyectos que no tengan Etiquetas.

5. Configurar los 
