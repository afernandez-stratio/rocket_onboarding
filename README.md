# CHEAT SHEET _ROCKET ONBOARDING_

# DOCUMENTACION


__[pegaso83](https://pegaso83.docs.stratio.com/)__: 
user:stratiouser1
password:7T8SUbT

__[next](https://next.docs.stratio.com/)__:
user:stratiouser1
password:7T8SUbT

__[Nueva Antora](https://docs.stratio.com/)__:
User/pass: El propio de LDAP

Es importante estar dado de alta en Jira para todo producto.

Para ello se necesita crear un Jira en el Team Jira:

Ejemplo: https://stratio.atlassian.net/browse/TJ-3641

# ENTORNOS

## TGZ's DCOS/MARATHON 

- nightlyrocket: http://daedalus-workspaces.int.stratio.com/daedalus-workspace-nightlyrocket.tgz

## K8s
- Oscar: http://keos-workspaces.int.stratio.com/keos-workspace-oscar.tgz
- twentythree: http://keos-workspaces.int.stratio.com/keos-workspace-twentythree.tgz

### URLS

# <==REVISAR==>

- PIT:
    - Gitlab: http://pitservices.labs.stratio.com:8089/ --> rocket/12345678
    - Nexus: http://pitservices.labs.stratio.com:8081 --> user: admin pass: 1234
    - Jenkins: http://pitservices.labs.stratio.com:8080/ -> admin/1234


- ENTORNO DEV:
https://bootstrap.xray.labs.stratio.com/
    - user: qa1owner
    - password: 1234
    - user: admin
    - password: 1234
    - Tenant: NONE y s000001

- ENTORNO OSCAR:
https://admin.k8s.oscar.labs.stratio.com
    - user admin 
    - pass 1234

- ENTORNO FULLENERO:
(/etc/hosts --> 10.200.21.26 fullereno04.labs.stratio.com)
https://bootstrap.fullereno.labs.stratio.com/
    - tenant: NONE
    - user: admin
    - password: 1234
    - https://fullereno04.labs.stratio.com/s000001-rocket-validation/
    - user: qa1owner
    - password: 1234

- ENTORNO NIGTHLY:
https://bootstrap.nightlyrocket.labs.stratio.com/


- INTELLIGENCE:
MULTIUSER:
    - Administrador 
    - https://bootstrap.xray.labs.stratio.com/service/s000001-asoriano-intelligence/

- ANALYTIC:
Usuario - https://public01.xray.labs.stratio.com/s000001-asoriano-intelligence/

- PGADMIN 
http://10.200.12.11:22923/login?next=%2F
User:pgadmin@stratio.com
Password:stratio

# CICD

Jenkins nos permite más o menos fácil ver los repos que tenemos activos aunque algunos no aplican
https://builder.int.stratio.com/

El ususario de login es el de LDAP

Jobs asociados a repos de Rocket: https://builder.int.stratio.com/job/Rocket/

Repos asociados al PIT pero incluye saas-universe y kubernetes-universe donde vamos metiendo los descriptores/reglas de CCT. Estos descriptores también están en nuestros repos:
https://builder.int.stratio.com/job/Universe/

En QA también utilizan alguno repo común adicional: https://builder.int.stratio.com/job/QA/

# PLATAFORMAR PROYECTO / EJECUTAR ROCKET LOCAL

## **REQUISITOS**

### Tener instalado DOCKER

Tenemos que tener instalado docker para hacer uso de imágenes

### Tener instalado SCALA / JDK

El proyecto hace uso de Java8 / Scala 2.12. Por lo que necesitamos instalar ambas.

En caso de ya tenerlas instaladas, ver si por defecto tenemos instalada la 8 si no tenemos que cambiarla

```bash
update-alternatives --config java
```

### Tener instalado Maven

Instalar en local Apache Maven. Bien con apt-get o instancia local descargada desde Apache Maven y seteando sus variables de entorno M2_HOME, JAVA_HOME, etc..

Más sencillo instalando con el gestor de paquetes

```bash
sudo apt update
sudo apt install maven
```
Hacemos check con el consabido:

```bash
mvn -v

Apache Maven 3.6.0
Maven home: /usr/share/maven
Java version: 1.8.0_342, vendor: Private Build, runtime: /usr/lib/jvm/java-8-openjdk-amd64/jre
Default locale: es_ES, platform encoding: UTF-8
OS name: "linux", version: "4.15.0-192-generic", arch: "amd64", family: "unix"
```

OJO con el archivo de configuración de maven (/usr/share/maven/conf/settings.xml), es es necesario poner el siguiente:

```bash
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
  <localRepository />
  <interactiveMode />
  <usePluginRegistry />
  <offline />
  <pluginGroups />
  <servers />
  <build>
    <pluginGroups>
      <pluginGroup>org.ensime.maven.plugins</pluginGroup>
    </pluginGroups>
  </build>
  <mirrors>
    <mirror>
      <id>stratio-snapshots</id>
      <url>http://sodio.int.stratio.com/repository/public</url>
      <mirrorOf>central</mirrorOf>
    </mirror>
  </mirrors>
  <profiles>
    <profile>
      <id>nexus</id>
      <activation>
        <activeByDefault>true</activeByDefault>
      </activation>
      <repositories>
        <repository>
          <id>central</id>
          <url>http://central</url>
          <releases>
            <enabled>true</enabled>
          </releases>
          <snapshots>
            <enabled>true</enabled>
          </snapshots>
        </repository>
        <repository>
          <id>thirdparty-snapshots</id>
          <url>http://sodio.int.stratio.com/repository/thirdparty-snapshots</url>
          <snapshots>
            <enabled>true</enabled>
          </snapshots>
        </repository>
      </repositories>
      <pluginRepositories>
        <pluginRepository>
          <id>central</id>
          <url>http://central</url>
          <releases>
            <enabled>true</enabled>
          </releases>
          <snapshots>
            <enabled>true</enabled>
          </snapshots>
        </pluginRepository>
      </pluginRepositories>
    </profile>
  </profiles>
</settings>
```

### Tener instalado npm local

Lo más sencillo es instalando y haciendo uso del NVM, también versiones superiores a las pintadas abajo:

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.2/install.sh | bash
nvm install 14.15.3
nvm alias rocket 14.15.3
nvm use rocket
```

### Tener instalado Miniconda3/Python

__Necesitamos instalar miniconda3__ para ejecutar el servidor

Podemos bajar el siguiente [script de instalación](https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh ) y ejecutarlo para hacer la instalación. Con miniconda te instala Python el cual también necesitamos

## **CODIGO**

### Github

Hacemos un fork de los siguientes 4 proyectos de stratio a nuestra cuenta de github de stratio:
- rocket-workspace
- rocket-core
- rocket-driver
- rocket-ui

Clonar el proyecto "rocket-workspace". 

```bash
git clone git@github.com:Stratio/rocket-workspace.git
```

En este punto los submódulos del proyecto rocket-workspace apuntan a github de stratio. Modificamos el archivo .gitmodules para hacer que los submódulos apunten a nuestro fork de estos proyectos. 
Por ejemplo:

```bash
[submodule "rocket-core"]
	path = rocket-core
	url = https://github.com/stratio/rocket-core.git
	branch = master

[submodule "rocket-driver"]
	path = rocket-driver
	url = https://github.com/stratio/rocket-driver.git
	branch = master

[submodule "rocket-ui"]
	path = rocket-ui
	url = https://github.com/stratio/rocket-ui.git
	branch = master
```
cambiar a:

```bash
[submodule "rocket-core"]
path = rocket-core
url = https://github.com/afernandez-stratio/rocket-core.git
branch = master

[submodule "rocket-driver"]
path = rocket-driver
url = https://github.com/afernandez-stratio/rocket-driver.git
branch = master

[submodule "rocket-ui"]
path = rocket-ui
url = https://github.com/afernandez-stratio/rocket-ui.git
branch = master
```

La primera vez necesitamos actualizar los submódulos:

```bash
git submodule update --remote
git submodule update --init --recursive
```
Por otro lado, si se está usando el IDE de IntellJ podemos crear un proyecto a partir del repositorio de github autorizando el acceso.

Debemos de apuntar en todos los proyectos a **master**

- rocket-driver
- rocket-core
- rocket-ui

### Creación de python_venv

1. Vamos al submódulo de rocket-core -> developer_utils -> python_env
2. Ejecutamos el archivo __python-dev-env-creation.sh__. Esto nos creará el entorno de python en el root del workspace que posteriormente se usará para la ejecución del servidor.
En mi caso: /home/antoniofernandez/develop/rocket/rocket-workspace/rocket-core/python_venv

### Maven

Una vez descargado el proyecto con sus submodulos git, vamos a hacer install de cada uno de forma individual ejecutando el siguiente comando

```maven
mvn clean -U install -DskipITs -DskipUTs -Dlicense.skip=true
```

## **EJECUTAR**

> DISCLAIMERS
> - Asegurar que el IDE (intelliJ o el que sea) tenga configurada Java 8 y no cualquier otra SDK. Así mismo asegurarse con la versión de Scala 2.12
>
>

Arrancar un postgres en local con Docker:

> docker run -d -e POSTGRES_HOST_AUTH_METHOD=trust -p 5432:5432 postgres:9.6

Posterior a tener aseguradas las versiones correctas, conda instalado y docker con postgres lanzado, nos vamos al proyecto de rocket-workspace:

### ROCKET SERVIDOR:

Si usamos intelliJ tenemos que crear una Run Configuration de tipo "Application":

- En Build and Run: __Java 8 / -cp serving-api__
- Main class: __com.stratio.sparta.serving.api.Sparta__
- Env args: __ENABLE_SCHEDULING_PLANNED_QUALITY_RULES=true;MLFLOW_CONDA_HOME=/Mi_ruta/miniconda3;SPARK_SECURITY_CUSTOM_CLASSLOADER_ENABLED=true;SPARTA_BOOTSTRAP_MODE=local;SPARTA_LOG_LEVEL=DEBUG;SPARTA_MLFLOW_LAUNCHER_CMD=/Mi_ruta/rocket-workspace/rocket-core/python_venv/bin/mlflow__
** Nota ** 
Cambiar "Mi_ruta" con lo que corresponda.

### TroubleShooting.

- __Si en la ejecución de modelos de mlProjects obtenemos el siguiente error:__

> MlFlow project execution has failed
>
> Exception:
> MlFlow project execution has failed
> /home/antoniofernandez/develop/rocket-workspace/rocket-core/python_venv/bin/python3.9: Error while finding module specification for 'rocket_python_driver.driver_launcher' (ModuleNotFoundError: No module named 'rocket_python_driver')

Seguramente ha habido algún problema en la instalación del driver. Para volver a ejecutarlo:

Comprobamos los entornos de python
>conda env list

Activamos el entorno .../python_venv
> conda activate /home/antoniofernandez/develop/rocket/rocket-workspace/rocket-core/python_venv

Una vez en el entorno del proyecto ejecutamos lo siguiente:
> YOUR-PATH -> rocker-core -> rocket-python-driver ejecutamos el siguiente comando: ./python-installer.sh ./target/ 3.0.0-SNAPSHOT (o la versión que corresponda dentro del directorio target).

- __Problemas al levantar el cluster de akka.__

Revisar los ficheros /etc/hostname y /etc/hosts.

Fichero /etc/hostname
> antoniofernandez

Fichero /etc/hosts
> 127.0.0.1	localhost
> 127.0.1.1	antoniofernandez
> #127.0.1.1	Stratio.unassigned-domain	Stratio

- __Al ejecutar Rocket -> No se encuentra la clase principal "com.stratio.sparta.serving.api.Sparta"__

En este caso hemos borrado la carpeta ".idea" del raíz del proyecto y reconfigurado todo de nuevo.

### ROCKET UI

En este punto tenemos 2 opciones. Podemos compilar vía maven el proyecto rocket-ui. El cual se ejecutaran los comandos de npm pero sin embargo irá más lento y perdemos live reload.

De cara a facilitar la ejecución podemos hacer uso de los siguientes scripts:

Para establecer node en el path, compilar y ejecutar
```
#!/bin/bash

PATH=[YOUR PATH]/rocket-workspace/rocket-ui/node/:$PATH
cd [YOUR PATH]/rocket-workspace/rocket-ui && npm i && npm run start:safe
```

Para ejecutar la UI únicamente

```
#!/bin/bash

PATH=[YOUR PATH]/rocket-workspace/rocket-ui/node/:$PATH
cd [YOUR PATH]/rocket-workspace/rocket-ui && npm start:safe
```

Levanta la UI en el puerto 4200 http://localhost:4200

### EJECUTAR MLPROJECT

Crear nuevo proyecto de tipo MLProject
MLFLow 

TIpo Example -> Por sencillez

Seguir los pasos del README que genera

Ir al catálogo y crear un dataset -> CREATE TABLE bla bla bla

Cambiar el HDFS a FileSystem local al csv del ejemplo wine_quality


### METODOLOGIA COLABORATIVA

1. Hacer FORK de stratio/rocket-core
2. git clone git@github.com:adelpozo-stratio/rocket-core.git
3. git remote -v

>     origin git@github.com:adelpozo-stratio/rocket-core.git (fetch)
>     origin git@github.com:adelpozo-stratio/rocket-core.git (push)


4. git remote add upstream git@github.com:Stratio/rocket-core.git
5. git remote -v

>   origin	git@github.com:adelpozo-stratio/rocket-core.git (fetch)

>   origin	git@github.com:adelpozo-stratio/rocket-core.git (push)

>   upstream	git@github.com:Stratio/rocket-core.git (fetch)

>   upstream	git@github.com:Stratio/rocket-core.git (push)

6. git pull upstream master

> Desde github.com:Stratio/rocket-core
> * branch                master     -> FETCH_HEAD
> * [nueva rama]          master     -> upstream/master
>Ya está actualizado.

7. git checkout -b feature/spark-conf-workers

---

# **VULNERABILIDADES**

Estamos utilzando __anchore__ para la detección de vulnerabilidades.
### Los jobs de anchore para comprobar si hay o no vunerabilidades se pasan:

- siempre que lanzamos una prerelease (en la release no se vuelve a pasar)
- siempre que lanzamos una milestone (aunque no bloquea sacarla)
- en las PRs si modificamos el Jenkinsfile ( cambiando el valor de ANCHORE_TEST = false)
- semanalmente para las imágenes que tenemos configuradas en Jenkins (podéis ver la ruta de directorios en la URL): https://builder.int.stratio.com/job/Anchore/job/Rocket/
- bajo demanda haciendo un build with parameters de este job https://builder.int.stratio.com/job/Anchore/job/Rocket/job/on-demand-vulns-report/ indicando un listado de imágenes (si no recuerdo mal una imágen por fila)


Un ejemplo del informe que saca el job de anchore: https://builder.int.stratio.com/job/Anchore/job/Rocket/job/on-demand-vulns-report/17/FAI[…]0Report_20rocket-mleap-microservice_3a3_2e0_2e0-SNAPSHOT/


Nos interesa consultar la sección de policy ya que es donde viene el listado de vulnerabilidades con gate action = stop que nos impedirían sacar una prerelease si no arreglamos antes la vulnerabilidad. 

## Procedimiento

Una vez que tenemos una vulnerabilidad hay varias posibilidades:

- se puede subir de versión sin riesgo y no afecta a posibles usuarios (ej subir de una versión 1.3.0 a 1.3.11)
- no poder subir de versión porque puede impactar al usuario (ej subir de python 3.7 a 3.8)
- no poder subir de versión porque son dependencias de módulos de Stratio como Spark/Crossdata que no - - - pueden subir por lo que sea o  bien librerías externas que todavía no han liberado nuevas versiones
otras

### Waivers

En caso de no poder subir versión hay que abrir un ticket a cybersec de tipo support request y justificando siempre la causa para no arreglar la vulnerabilidad con la información que indican a continuación

>Con el fin de optimizar/cuantificar el proceso, el equipo de Cybersec sólo atenderá peticiones de apertura de waivers de Anchore a través de issues de tipo "Support Request" abiertas al equipo de Cyber (CYB) en JIRA, y estas deberán contener la URL de jenkins del pipeline fallido así como el nombre de la policy de Anchore que se debe modificar.

Este es el repo donde cybersec añade las excepciónes de vulnerabilidades: https://github.com/Stratio/anchore-policies

En principio intenta gestionar las dependencias de modo que si añaden una vulnerabilidad a la __whitelist__ en Spark también se aplica sobre los módulos que dependen de Spark pero no siempre cubre todos los casos.

### **Ejemplos de tickets**

https://builder.int.stratio.com/job/Rocket/job/discovery/view/change-requests/job/PR-184/3/

Ejemplo de service request: > https://stratio.atlassian.net/browse/CYB-64224

Creado nuevo copiando el de intelligence: > https://stratio.atlassian.net/browse/CYB-66650

Si están listos nos dicen que en uno de los casos no dependemos de Spark pero esperemos que lo den por bueno

---
### Prueba real en imágenes analytic

1. Sería lanzar este job => bajo demanda haciendo un build with parameters de este job https://builder.int.stratio.com/job/Anchore/job/Rocket/job/on-demand-vulns-report/ indicando un listado de imágenes (si no recuerdo mal una imágen por fila)


2. Pasar las imágenes de anaytic por linea (Specify one tag per line.)
    - Imágenes:
        - qa.int.stratio.com/stratio/analytic-environment:3.0.0-SNAPSHOT
        - qa.int.stratio.com/stratio/analytic-environment-light:3.0.0-SNAPSHOT

3. Al pasar el job se ven vulnerabilidades graves (STOP)
4. El propio reporte te indica que se puede subir la versión de la dependencia a las 1.31 para solventar esta vulnerabilidad. (HIGH Vulnerability found in non-os package type (java) - /opt/spark/dist/jars/snakeyaml-1.26.jar (fixed in: 1.31)(GHSA-3mc7-4q67-w48m - https://github.com/advisories/GHSA-3mc7-4q67-w48m))

>(analytic: https://builder.int.stratio.com/job/Anchore/job/Rocket/job/on-demand-vulns-report/19/FAIL_20-_20Anchore_20Report_20analytic-environment_3a3_2e0_2e0-SNAPSHOT/

>analytic-light: https://builder.int.stratio.com/job/Anchore/job/Rocket/job/on-demand-vulns-report/19/FAIL_20-_20Anchore_20Report_20analytic-environment-light_3a3_2e0_2e0-SNAPSHOT/ )

5. La imagen: https://github.com/Stratio/intelligence-analytic-environment extiende de la imagen base https://github.com/Stratio/intelligence-docker-base la cual es la que lleva esa dependencia. Tiene que ver con la distribición de Spark /opt/spark/dist/jars/
6. Abrimos en este caso un Waiver a CyberSec. https://stratio.atlassian.net/browse/CYB-66661

