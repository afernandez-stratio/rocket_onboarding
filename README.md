# CHEAT SHEET _ROCKET ONBOARDING_

---


# DOCUMENTACION

__pegaso83.docs.stratio.com__: 
user:stratiouser1
password:7T8SUbT

__next.docs.stratio.com__:
user:stratiouser1
password:7T8SUbT

__Nueva Antora__:
https://docs.stratio.com/
User/pass: El propio de LDAP

# ENTORNOS

## TGZ's DCOS/MARATHON 

- nightlyrocket: http://daedalus-workspaces.int.stratio.com/daedalus-workspace-nightlyrocket.tgz

## K8s
- Oscar: http://keos-workspaces.int.stratio.com/keos-workspace-oscar.tgz
- twentythree: http://keos-workspaces.int.stratio.com/keos-workspace-twentythree.tgz

### URLS

==REVISAR==

- PIT:
Gitlab: http://pitservices.labs.stratio.com:8089/ --> rocket/12345678
Nexus: http://pitservices.labs.stratio.com:8081 --> user: admin pass: 1234
Jenkins: http://pitservices.labs.stratio.com:8080/ -> admin/1234

- ENTORNO DEV:
https://bootstrap.xray.labs.stratio.com/
user: qa1owner
password: 1234
user: admin
password: 1234
Tenant: NONE y s000001

- ENTORNO OSCAR:
https://bootstrap.oscar.hetzner.stratio.com/#/login
user admin 1234

- ENTORNO FULLENERO:
(/etc/hosts --> 10.200.21.26 fullereno04.labs.stratio.com)
https://bootstrap.fullereno.labs.stratio.com/
tenant: NONE
user: admin
password: 1234
https://fullereno04.labs.stratio.com/s000001-rocket-validation/
user: qa1owner
password: 1234

- ENTORNO NIGTHLY:
https://bootstrap.nightlyrocket.labs.stratio.com/


- INTELLIGENCE:
MULTIUSER:
Administrador - https://bootstrap.xray.labs.stratio.com/service/s000001-asoriano-intelligence/

- ANALYTIC:
Usuario - https://public01.xray.labs.stratio.com/s000001-asoriano-intelligence/

- PGADMIN 
http://10.200.12.11:22923/login?next=%2F
User:pgadmin@stratio.com
Password:stratio

# CICD

Jenkins nos permite más o menos fácil ver los repos que tenemos activos aunque algunos no aplican
https://builder.int.stratio.com/

Jobs asociados a repos de Rocket: https://builder.int.stratio.com/job/Rocket/

Repos asociados al PIT pero incluye saas-universe y kubernetes-universe donde vamos metiendo los descriptores/reglas de CCT. Estos descriptores también están en nuestros repos:
https://builder.int.stratio.com/job/Universe/

En QA también utilizan alguno repo común adicional: https://builder.int.stratio.com/job/QA/

# PLATAFORMAR PROYECTO / EJECUTAR ROCKET LOCAL

## **REQUISITOS**

### SCALA / JDK

Para compilar se requiere JDK 8. Ver si por defecto tenemos instalada la 8 si no tenemos que cambiarla

```bash
update-alternatives --config java
```

### Maven local

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

### Tener instalado npm local

Lo más sencillo es instalando y haciendo uso del NVM

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.2/install.sh | bash
nvm install 14.15.3
nvm alias rocket 14.15.3
nvm use rocket
```

## **CODIGO**

### Github

Clonar el proyecto de rocket con los submódulos de git. 

```bash
git clone git@github.com:Stratio/rocket-workspace.git
```

Debemos de apuntar en todos los proyectos a **master**

- rocket-driver
- rocket-core
- rocket-ui

### Maven

Una vez descargado el proyecto con sus submodulos git, vamos a hacer install de cada uno de forma individual ejecutando el siguiente comando

```maven
mvn clean -U install -DskipITs -DskipUTs -Dlicense.skip=true
```

## **EJECUTAR**

Compilar y arrancar UI

```
#!/bin/bash

PATH=[YOUR PATH]/rocket-workspace-2.2/rocket-ui/node/:$PATH
cd [YOUR PATH]/rocket-workspace-2.2/rocket-ui && npm i && npm run start:safe
```

Ejecutar

```
#!/bin/bash

PATH=[YOUR PATH]/rocket-workspace-2.2/rocket-ui/node/:$PATH
cd [YOUR PATH]/rocket-workspace-2.2/rocket-ui && npm start
```

# **VULNERABILIDADES**

Estamos utilzando __anchore__ para la detección de vulnerabilidades.
### Los jobs de anchore para comprobar si hay o no vunerabilidades se pasan:

- siempre que lanzamos una prerelease (en la release no se vuelve a pasar)
- siempre que lanzamos una milestone (aunque no bloquea sacarla)
- en las PRs si modificamos el Jenkinsfile ( cambiando el valor de ANCHORE_TEST = false)
- semanalmente para las imágenes que tenemos configuradas en Jenkins (podéis ver la ruta de directorios en la URL): https://builder.int.stratio.com/job/Anchore/job/Rocket/
- bajo demanda haciendo un build with parameters de este job https://builder.int.stratio.com/job/Anchore/job/Rocket/job/on-demand-vulns-report/ indicando un listado de imágenes (si no recuerdo mal una imágen por fila)


Un ejemplo del informe que saca el job de anchore: https://builder.int.stratio.com/job/Anchore/job/Rocket/job/on-demand-vulns-report/17/FAI[…]0Report_20rocket-mleap-microservice_3a3_2e0_2e0-SNAPSHOT/


Nos interesa consultar la sección de policy ya que es donde viene el listado de vulnerabilidades con gate action = stop que nos impedirían sacar una prerelease si no arreglamos antes la vulnerabilidad. En la captura podéis ver por ejemplo que el CVE-2022-0778+libssl1.1 bloquearía la prerelease, podéis ver información sobre como arreglar el CVE: "HIGH Vulnerability found in os package type (dpkg) - libssl1.1 (fixed in: 1.1.1-1ubuntu2.1~18.04.15)(CVE-2022-0778 - http://people.ubuntu.com/~ubuntu-security/cve/CVE-2022-0778)" y si es un jar suele venir la ruta, etc..

Una vez que tenemos una vulnerabilidad hay varias posibilidades:

- se puede subir de versión sin riesgo y no afecta a posibles usuarios (ej subir de una versión 1.3.0 a 1.3.11)
- no poder subir de versión porque puede impactar al usuario (ej subir de python 3.7 a 3.8)
- no poder subir de versión porque son dependencias de módulos de Stratio como Spark/Crossdata que no - - - pueden subir por lo que sea o  bien librerías externas que todavía no han liberado nuevas versiones
otras


En caso de no poder subir versión hay que abrir un ticket a cybersec de tipo support request y justificando siempre la causa para no arreglar la vulnerabilidad con la información que indican a continuación

>Con el fin de optimizar/cuantificar el proceso, el equipo de Cybersec sólo atenderá peticiones de apertura de waivers de Anchore a través de issues de tipo "Support Request" abiertas al equipo de Cyber (CYB) en JIRA, y estas deberán contener la URL de jenkins del pipeline fallido así como el nombre de la policy de Anchore que se debe modificar.

Este es el repo donde cybersec añade las excepciónes de vulnerabilidades: https://github.com/Stratio/anchore-policies
En principio intenta gestionar las dependencias de modo que si añaden una vulnerabilidad a la whitelist en Spark también se aplica sobre los módulos que dependen de Spark pero no siempre cubre todos los casos.

### **Ejemplo**

https://builder.int.stratio.com/job/Rocket/job/discovery/view/change-requests/job/PR-184/3/

Ejemplo de service request: > https://stratio.atlassian.net/browse/CYB-64224

Creado nuevo copiando el de intelligence: > https://stratio.atlassian.net/browse/CYB-66650

Si están listos nos dicen que en uno de los casos no dependemos de Spark pero esperemos que lo den por bueno
