# Deploying Appsody app in Openshift using Tekton pipeline
 (Developing Springboot Fruits app in Appsody and Deploying in Openshift using Tekton pipeline)

## Prerequisites

* Cloud Pak for Applications installed on Openshift cluster
* appsody CLI
* oc CLI
* git


## Add Kabanero Collection to appsody

1. Appsody should have kabanero collections. Check it by using the below command.

```
$ appsody repo list

NAME      	URL                                                                                    
*incubator	https://github.com/appsody/stacks/releases/latest/download/incubator-index.yaml        
kabanero  	https://github.com/kabanero-io/collections/releases/download/v0.1.2/kabanero-index.yaml
```

There should be an entry with the name `kabanero`

2. If the entry doesn`t exists, you can add it using the below command.

```
appsody repo add kabanero https://github.com/kabanero-io/collections/releases/download/v0.1.2/kabanero-index.yaml
```

3. Look at the available stacks in kabanero

```
$ appsody list kabanero

REPO    	ID               	VERSION  	TEMPLATES        	DESCRIPTION                                              
kabanero	java-microprofile	0.2.11   	*default         	Eclipse MicroProfile on Open Liberty & OpenJ9 using Maven
kabanero	java-spring-boot2	0.3.9    	*default, kotlin 	Spring Boot using OpenJ9 and Maven                       
kabanero	nodejs           	0.2.5    	*simple          	Runtime for Node.js applications                         
kabanero	nodejs-express   	0.2.5    	*simple, skaffold	Express web framework for Node.js                        
kabanero	nodejs-loopback  	0.1.4    	*scaffold        	LoopBack 4 API Framework for Node.js        
```

There should be a stack called `springboot`.


## Create Appsody Application

1. Create a directory and change to that directory

```
mkdir appsody-fruits-app

cd appsody-fruits-app
```

2. Create appsody project using `springboot` stack

```
appsody init kabanero/java-spring-boot2
```

3. The following files might have been created

![Appsody files](images/01-appsody-files.png?raw=true "Appsody files")


## Build and Run the Application locally

1. Build the application locally using the below command.

```
appsody build
```

2. This will build the docker image locally and ends with the below output.

```
[Docker]  ---> Running in 318682904bbf
[Docker] Removing intermediate container 318682904bbf
[Docker]  ---> d779e804797e
[Docker] Successfully built d779e804797e
[Docker] Successfully tagged dev.local/appsody-fruits-app:latest
Built docker image dev.local/appsody-fruits-app
```

3. Run the application using appsody.

```
appsody run
```

4. You can see the console logs like the below

```
[Container] 2020-01-10 13:46:48.912  INFO 161 --- [  restartedMain] o.s.b.d.a.OptionalLiveReloadServer       : LiveReload server is running on port 35729
[Container] 2020-01-10 13:46:48.935  INFO 161 --- [  restartedMain] o.s.b.a.e.web.EndpointLinksResolver      : Exposing 4 endpoint(s) beneath base path '/actuator'
[Container] 2020-01-10 13:46:49.135  INFO 161 --- [  restartedMain] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
[Container] 2020-01-10 13:46:49.147  INFO 161 --- [  restartedMain] application.Main                         : Started Main in 11.974 seconds (JVM running for 14.377)
```

5. Access the applicaiton using the below url.

```
    http://localhost:8080
    http://localhost:8080/actuator/health
    http://localhost:8080/actuator/liveness
    http://localhost:8080/actuator/metrics
    http://localhost:8080/actuator/prometheus
```

6. Stop the application.

```
appsody stop
```

## Deploy App in Openshift using Tekton

### 1. Create project in Openshift

1. Login into your Redhat Openshift Container Platform like below

```
oc login --token=NIqa12ewuV2AR-ZTAbG0v --server=https://api.ganyellow.os.fyre.ibm.com:6443
```

2. Create new project in Openshift

```
oc new-project appsody-fruits-app-pro
```
### 2. Create manifest files

1. Create application manifest files using the below command. 

```
appsody deploy --generate-only
```

It might have created `app-deploy.yaml` additionally.

2. Add namespace `appsody-fruits-app-pro` in `app-deploy.yaml` under `name` like below

```
  name: appsody-fruits-app
  namespace: appsody-fruits-app-pro
```

3. Change `applicationImage` in `app-deploy.yaml` like below

```
  # applicationImage: dev.local/appsody-fruits-app
  applicationImage: gandhicloud/appsody-fruits-app
```

### 3. Push the source to GIT

1. Create a new repository `appsody-fruits-app` in GitHub using the webUI.

Ex:  git repo
```
https://github.ibm.com/JeyaGandhi/appsody-fruits-app.git
```

2. Run the below commands to init, add, commit and push this application source to the git hub.

You may need to give GIT user and personal acccess token to push the code. Please refer the URL given in the reference sections for the more details on personal acccess token.

```
git init
git add .
git commit -m "initial commit"
git remote add origin https://github.ibm.com/JeyaGandhi/appsody-fruits-app.git
git push -u origin master
```

### 4. Create Webhook in Tekton

1. Open the Tekton webUI.

2. Choose "Webhooks" menu on the left 

3. Choose "Add Webhook" button

4. Add the below details in the popup based on your env/config.

| Property Name  | Values | 
| ------------- | ------------- |
| Name  | webhook-appsody-fruits-app  | 
| Repository URL  | https://github.ibm.com/JeyaGandhi/appsody-fruits-app.git  | |
| Access Token  | Select the git-hub access token from the list. Otherwise click + button to enter Github user and Personal access token|
|   |  | 
| Namespace  | kabanero  |
| Pipeline  | java-spring-boot2-build-push-deploy-pipeline  |
| Service account  | kabanero-operator  |
| Docker Registry  | index.docker.io/gandhicloud  |

![Add Tekton Webhook](images/02-add-webhook.png?raw=true "Add Tekton Webhook" )

5. Webhook is created.

![Webhook](images/03-webhook.png?raw=true "Webhook")


### 5. Create Docker-Registry Secret in Tekton

1. Open the Tekton webUI.

2. Choose "Secrets" menu on the left 

3. Choose "Add Secret" button

4. Add the below details in the popup based on your env/config.

| Property Name  | Values |
| ------------- | ------------- |
| Name  | gandhicloud-secret  |
| Namespace  | kabanero  |
| Access To  | Docker Registry  |
| Username  | gandhicloud  |
| Password/Token  | <<<Docker-registry-pwd>>>  |
| Service account  | kabanero-operator  |
| Server URL | https://index.docker.io/gandhicloud  |

![Docker Registry Secret](images/04-docker-registry-secret.png?raw=true "Docker Registry Secret" )


### 6. Modify and Push the application source code 

1. Do some modification in the index.html found in the below path

```
/src/main/resources/public/index.html
```

2. Commit and push the changes to the git.

```
git add .
git commit -m "first change"
git push -u origin master
```

### 7. Verify the Tekton pipeline is running and completed 

1. In Tekton webUI, click on "PipelineRuns" menu.

2. There should be an entry with "webhook-appsody-fruits-app-xxxxxxxxxxx"

![Tekton Pipeline](images/05-tekton-pipeline.png?raw=true "Tekton Pipeline")

3. Click on the pipeline, it opens the Tekton pipeline details.

Wait until all the tasks get completed. 

The images are rebuild and pushed to the registry and openshift update the new image.

![Tekton Pipeline Details](images/06-tekton-pipeline-details.png?raw=true "Tekton Pipeline Details")


### 8. Verify the application is running 

1. Get the application route url by running the below command

```
oc get route
NAME                  HOST/PORT                                                                PATH   SERVICES              PORT       TERMINATION   WILDCARD
appsody-fruits-app2   appsody-fruits-app2-appsody-fruits-app2.apps.ganyellow.os.fyre.ibm.com          appsody-fruits-app2   8080-tcp                 None

```

2. Open the url in the browser and you can see the changes.

```
http://appsody-fruits-app2-appsody-fruits-app2.apps.ganyellow.os.fyre.ibm.com
```

We have successfully created an application using appsody and deployed in to openshift through the tekton pipeline.


## Troubleshooting

1. Git push is not prompting for the username/pwd or it is giving permission denied error.

Run the below command to clear the user credentials cache.
```
git config --global --unset credential.helper
```

Now try the `git push` again.

2. When tekton fails with error `pod has unbound immediate PersistentVolumeClaims (repeated 3 times)`, you need to create pv.

You can run the below command to create pv.

```
oc apply -f pv.yaml
```


## References

1. How to create personal acccess token to git hub repo.

See the section `Generating the Token` 
```
https://ibm-cloud-architecture.github.io/Learning-Kabanero-101/web/1.0.0/tekton-credentials-github.html
```
