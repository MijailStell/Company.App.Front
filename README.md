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
```
![3 Agents](https://user-images.githubusercontent.com/1031887/127592369-545251d2-2908-40f9-8917-72550af66632.PNG)
```
#Acceder a https://dev.azure.com/{username}/_settings/deploymentpools y crear un Pool de despliegue on-premise llamado IIS-Server
```
![3 DeploymentGroup](https://user-images.githubusercontent.com/1031887/127592393-59a2daef-1588-434b-80bb-e45843d1f489.PNG)
```
#Acceder a https://dev.azure.com/{username}/DevOps/_settings/adminservices y crear una conexión hacia sonarqube server
```
![3 ServiceConnection](https://user-images.githubusercontent.com/1031887/127592788-1c501b2b-928b-4798-9ad7-e483d8dbec8f.PNG)
```
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
  
- task: CopyPublishBuildArtifacts@1  
  displayName: 'Copy Publish Artifact: $(artifactName)'  
  inputs:  
    CopyRoot: '$(artifactName)\dist'
    Contents: '**'  
    ArtifactName: $(artifactName)  
    ArtifactType: Container 

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
![1 Summary](https://user-images.githubusercontent.com/1031887/127594332-5da51330-2580-4393-894b-e19567cd7002.PNG)
![3 Release](https://user-images.githubusercontent.com/1031887/127594338-f222e4dc-a665-40f2-8b93-49d89325071c.PNG)
![4 SonarService](https://user-images.githubusercontent.com/1031887/127594343-99e3f395-2bed-4d01-ac3a-bb4a5f24fc56.PNG)
![5 Sonarqube](https://user-images.githubusercontent.com/1031887/127594346-48705793-889c-43c7-b757-28dd81b21d1a.PNG)

##  -------  Continuous Delivery on Premise  -------
```
#Acceder a https://dev.azure.com/{username}/_settings/deploymentpools y crear un Pool de despliegue on-premise llamado IIS-Server
#Acceder a https://dev.azure.com/{username}/DevOps/_release y crear un pipeline
#Seleccionar IIS website deployment y colocarle de nombre "devfront"
#Elegir tab "tasks"
Sección dev:
	WebSiteName: Company.App.Front
	Binding Port: 3003
Sección IIS Deployment
	Deployment Group: IIS Server
IIS Web Ap Manage
	Physical path: %SystemDrive%\inetpub\wwwroot\Company.App.Front
	Create or update app pool: check
	Application Pool:
		Name: Company.App.Front
		.NET version: No manage code
IIS Web App Deploy
	Remove Additional Files at Destination: check
	
#Renombrar a Company.App.Front
#Asignar Artifact. 
	Project: DevOps
	Source: {username}.Company.App.Front
	Finalmente agregar.
#Seleccionar artefacto y 
	Continuous deployment trigger: activado
```
![1 Deploy](https://user-images.githubusercontent.com/1031887/127594360-5380e74e-44f1-440a-8bd6-2e11f6924a90.PNG)
![7 IISFront](https://user-images.githubusercontent.com/1031887/127594698-dc2488bf-d918-4563-a4a5-6cf5db9f5112.PNG)
![7 IISFront2](https://user-images.githubusercontent.com/1031887/127594703-a9775c62-a99d-4ca1-93d6-1fb0c3527d70.PNG)
