### Pipeline scripted

* Apoyarse en la opción `Pipeline syntax->Snippet Generator`

1. En un editor copiar el `Jenkinsfile` del repositorio `some-code` y editarlo para transformarlo de `declarativo` a `scripted`.

2. Tener en cuenta que al no existir el `post` en scripted hay que crear un stage normal para este stage.

3. Crear un nuevo Job de tipo `Pipeline` y copiar en él el Jenkinsfile que hemos creado.

4. Ejecutar un build de este pipeline y comprobar los resultados.
