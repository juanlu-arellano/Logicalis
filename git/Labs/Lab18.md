1. Crear respositorio

mkdir dir
cd dir
git init

2. Crear fichero Gemfile

vi Gemfile
source "https://rubygems.org"

gem "jekyll"

3. Crear fichero index.html

$ vi index.html

4. Crear fichero .gitlab-ci.yaml

Hacer pruebas con el fichero añadiendo cosas y haciendo push

5. Proteger la rama master para que no se pueda hacer push

6. Crear la rama dev indicando un cambio de version y comprobar que pasa los tests pero no se despliega la nueva Version

7. Crear el merge request y comprobar que se ejecuta el pipeline y que se despliega la nueva versión.
