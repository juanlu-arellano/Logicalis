### Matrix Pipeline para múltiples plataformas y navegadores

Definición de un Pipeline en el que queremos hacer un build y test para diferentes plataformas y navegadores en paralelo.

1. Vamos a definir una matriz que tiene dos ejes: PLATFORM que tendra 3 valores y BROWSER que tendrá cuatro valores. Lo que hace un total de 12 celdas

       pipeline {
           agent none
           stages {
               stage('BuildAndTest') {
                   matrix {
                       agent any
                       axes {
                           axis {
                               name 'PLATFORM'
                               values 'linux', 'windows', 'mac'
                           }
                           axis {
                               name 'BROWSER'
                               values 'firefox', 'chrome', 'safari', 'edge'
                           }
                       }
                       stages {
                           stage('Build') {
                               steps {
                                   echo "Do Build for ${PLATFORM} - ${BROWSER}"
                               }
                           }
                           stage('Test') {
                               steps {
                                   echo "Do Test for ${PLATFORM} - ${BROWSER}"
                               }
                           }
                       }
                   }
               }
           }
       }

2. Crear un job de tipo Pipeline que se llame por ejemplo `matrix` y configurar el pipeline del punto 1.

3. Ejecutar un build y observar la salida de la consola.

4. Abrir el `Blue Ocean` y observar como las celdas se ejecutan en paralelo.

5. Modificar el Pipeline para excluir las siguientes combinaciones: `linux-safari` y cualquier plataforma que no sea `Windows` con el navegador de `Edge`:

       excludes {
           exclude {
               axis {
                   name 'PLATFORM'
                   values 'linux'
               }
               axis {
                   name 'BROWSER'
                   values 'safari'
               }
           }
           exclude {
               axis {
                   name 'PLATFORM'
                   notValues 'windows'
               }
               axis {
                   name 'BROWSER'
                   values 'edge'
               }
           }
       }

6. Ejecutar un build y observar la salida de la consola. Ver como ahora no se ejecutan los stages para las combinaciones excluidas.

7. Ahora vamos a modificar el Pipeline para configurar un parametro de tipo choice que nos permita elegir la plataforma:

       parameters {
           choice(name: 'PLATFORM_FILTER', choices: ['all', 'linux', 'windows', 'mac'], description: 'Run on specific platform')
       }

8. Ejecutar un build para que se añadan los parametros al Pipeline.

9. Ir a `Manage Jenkins -> Manage Nodes and Clouds` y añadir las siguientes etiquetas al master `linux-agent`, `mac-agent`, `windows-agent`.

10. A continuación, modificar el Pipeline para añadir una directiva `when` que añadirá una condición:

        when { anyOf {
            expression { params.PLATFORM_FILTER == 'all' }
            expression { params.PLATFORM_FILTER == env.PLATFORM }
        } }

11. Ejecutar un nuevo Build seleccionando una de las opciones, por ejemplo `linux` y ver en la consola que para las celdas que no cumplen la condición no se ejecuta el Pipeline.
