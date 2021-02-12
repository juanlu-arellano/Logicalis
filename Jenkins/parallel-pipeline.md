### Ejecución de Stages pararelos

1. Crear un Folder llamado "Parallel".

2. Dentro del folder crear un Proyecto de tipo Pipeline nombre `dec-parallel-pipeline` con el siguiente pipeline declarativo:

       pipeline {
          agent any
          stages {
              stage('before') {
                  steps {
                      println("before")
                  }
              }
              stage('parallel') {
                  parallel {
                      stage('apple') {
                        agent { label 'master' }
                          steps {
                              println("apple 1")
                              sleep(20 * Math.random())
                              println("apple 2")
                          }
                      }
                      stage('banana') {
                          agent { docker { image 'centos:latest' } }
                          steps {
                              println("banana 1")
                              sleep(20 * Math.random())
                              println("banana 2")
                          }
                      }
                      stage('peach') {
                          steps {
                              println("peach 1")
                              sleep(20 * Math.random())
                              println("peach 2")
                          }
                      }
                  }
              }
              stage('after') {
                  steps {
                      println("after")
                  }
              }
          }
       }

3. Ejecutar un build y comprobar que se ejecuta correctamente. Ver la salda de la consola.

4. Ir a `Blue Ocean` y ejecutar el pipeline desde ahi y comprobar como los stages se ejecutan en paralelo.

5. Dentro del mismo folder crear otro Pipeline que se llame `scrip-parallel-pipeline`.

6. Modificar el Pipeline del paso 2 a sintaxis `scripted` y copiarlo dentro del nuevo Pipeline

7. Ejecutar un build y comprobar la ejecución. Echar un vistazo a la consola del build.
- Ir a blue ocean y ejecutar desde ahi para que se vea q se ejecuta en paralelo.
