# Company.App.Front

##  -------  Git and manage Code  -------

```
#Crear directorio base de los proyectos y vincular cambios con github
mkdir Company.App.Front
cd Company.App.Front
echo "# Company.App.Front" >> README.md
git init
git add .
git commit -m "add readme"
git branch -M master
git remote add origin https://github.com/{username}/Company.App.Front.git
git push -u origin master

#Crear App Angular call front
ng new front
#Modo strict? press:
y
#Modo routing? press:
y
#Modo style? press:
scss
#Acceder a la aplicación llamada front
cd front
#Validar ejecución de aplicación
ng serve --open --port 4200

#Subir cambios a github

cd ..
git add .
git commit -m "basic template"
git push -u origin master

```

##  -------  Continuous Integration on Premise  -------
```
#Acceder a https://dev.azure.com/{username}/_settings/agentpools?poolId=1&view=jobs y crear un agente on-premise llamada DevOps
#Acceder a https://dev.azure.com/{username}/_settings/deploymentpools y crear un Pool de despliegue on-premise llamado IIS-Server
#Acceder a https://dev.azure.com/{username}/DevOps/_settings/adminservices y crear una conexión hacia sonarqube server
#Acceder a https://dev.azure.com/{username}/DevOps/_build
#Crear pipeline
#Seleccionar repositorio GitHub
#Conectar github con azuredevops
#Elegir plantilla: "Starter Pipeline"
#Pegar el siguiente contenido:

trigger:
- master

pool:
  name: DevOps
  demands: npm

variables:
  artifactName: 'front'

steps:
- task: Npm@0
  displayName: 'npm install'
  inputs:
    cwd: '$(artifactName)'

- task: Npm@0
  displayName: 'npm build'
  inputs:
    cwd: '$(artifactName)'
    command: run
    arguments: 'build --prod'


```

##  -------  Sonarqube on Continuous Integration  -------
```

cd front
echo sonar.projectKey=Company.App.Front >> sonar-project.properties

#Agregar el siguiente item en archivo .gitignore
.scannerwork/

#Obtener la última versión del azure-pipelines.yml
cd ..
git pull

#Subir cambios a github
git add .
git commit -m "basic template"
git push -u origin master

#Ir al archivo azure-pipelines.yml y agregar

- task: SonarQubePrepare@4
  inputs:
    SonarQube: 'SonarQube'
    scannerMode: CLI
    configFile: '$(artifactName)\sonar-project.properties'

- task: SonarQubeAnalyze@4

- task: SonarQubePublish@4

#Guardar y ejecutar. Se realizará la CI en azuredevops.
```
