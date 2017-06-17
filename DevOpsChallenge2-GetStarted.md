# DevOps Challenge \#2 #
In this challenge, you will move the ASP.NET application to a Docker container.
After you have created and tested the image locally, you will set up an Azure Container Registry and use the build pipeline
to build and publish the image to this registry.
Finally you will publish the container to an already created Azure Container Service cluster using the Kubernetes user interface.

When you successfully completed the challenge, your webapplication is running in a Docker container on a cluster.

## Content ##
* [Pre-requisites](#pre-requisites)
* [Getting started](#getting-started)
* [Achievements](#achievements)
* [Bonus Goals](#bonus-goals)
* [Resources](#resources)

## Pre-requisites ##
* Pipeline per team
* Visual Studio 2017 Enterprise (with the Coded UI test component)
* Docker for Windows [installed](https://download.docker.com/win/stable/InstallDocker.msi)
* Azure Subscription
* Team Project for venue with git repo per challenge team; VSTS account per participant
* Participants need administrator role on the Default Agent Pool (to be able to install a local build agent)
* Participants need administrator rights to add new service end-points in VSTS
* Azure Database for ASP.NET Music Store (created in Challenge 1)
* Docker Integration VSTS extension from the Azure Marketplace
* Base Docker image downloaded (pulled) for achievements  ```docker pull microsoft/aspnet```

## Getting started ##
* [Verify the Docker installation and explore the application](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine)
* Clone the started code from [https://github.com/GlobalDevOpsBootcamp/challenge1](https://github.com/GlobalDevOpsBootcamp/challenge1) to your VSTS project as described in challenge1
* Clone or download the files for challenge 2 from [https://github.com/GlobalDevOpsBootcamp/challenge2](https://github.com/GlobalDevOpsBootcamp/challenge2)

## Achievements ##
|#| Achievement   | Maximum score |
|---|---------------|:---------------:|
|1| Configure a build agent | 25 |
|2| Create a Dockerfile and build a local Docker image with the webapplication installed | 50 |
|3| Set up an Azure Container Registry | 25 |
|4| Create a build definition for your containerized application | 50 |
|5| Set up a release pipeline that consumes the container from your Azure Container Registry | 50 |


## Bonus Goals ##
|#| Bonus Goal   | Maximum score |
|---|---------------|:---------------:|
|1| Containerize SQL server and connect it to the webapplication container (with docker compose) | 50 |
|2| Incorporate unit tests | 25 |
|3| Clean-up environment after release | 25 |
|4| Make sure pipeline can deploy if the previous release failed | 25 |
|5| Setup continuous deployments | 25 |
|6| Add an additional environment | 25 |
|7| Integrate (Coded) UI tests | 50 |
|8| Setup and Use Kubernetes on Azure Container Sevrice to run your container and let your Azure WebApp website use this. | 200 |

## Achievement #1 - Configure a build agent ##
Install a local build agent on your Azure Virtual Machine so you can build locally. This is the same achievement as in challenge 1. If you already completed challenge 1 then you will not receive extra points.

[Learn about configuring a Build Agent](https://www.visualstudio.com/en-us/docs/build/concepts/agents/agents#install-and-connect-to-team-services-and-tfs-2017)

Achievement Unlocked. Your are now a Build Agent Installation Guru!

## Achievement #2 - Create a Dockerfile and build a local Docker image with the webapplication installed ##

What do we need to rollout an ASP.NET website to a windows docker container? To run an ASP.NET 4.X website you need the following things:

* The Operating system with IIS installed
* ASP.NET 4.X installed
* Webdeploy installed
* If you use the code from challenge 1, please remember to update the connection string of the web application to point to the SQL Azure Database that you created

To learn about the Docker commands, use the following starting points
* https://docs.docker.com/docker-for-windows/
* https://docs.docker.com/get-started/#a-brief-explanation-of-containers
* https://github.com/XpiritBV/mini-hacks/tree/master/Setup-an-IIS-enabled-Docker-Image
* https://www.katacoda.com/courses/docker
* https://fluentbytes.com/how-to-work-around-docker-compose-dns-issue-with-docker-on-windows/#more-8651
* https://fluentbytes.com/using-docker-on-windows-in-vsts-build-and-release-management/

To build the container with IIS, ASP.NET and Webdeploy the following steps are needed
* Validate that the docker image microsoft/aspnet (this image contains OS with IIS and ASP.NET) is on your local machine by using the command   ```docker images```
you should see in the list microsoft/aspnet.
If it is not there you can get it by using the command ```docker pull micrsoft/aspnet```
**Be careful here, if you run the pull command, then this can take an extensive amount of time (20 minutes +)**, so we pulled the image for you and it should already be on your machine.
**We learned that the docker pull will pull a new image that is updated a day ago by MSFt and it will break the challange as described here. So please don't execute the pull command!!!**

* Get the docker file from the [GitHub Repo for Challenge 2][https://github.com/GlobalDevOpsBootcamp/challenge2](https://github.com/GlobalDevOpsBootcamp/challenge2)
* Create publishing profile for web application
  * In Visual Studio right click the MvcMusicStore project and click Publish
  * Create new profile and select IIS, FTP, etc.
  * Select Webdeploy **package as Publish** method and set location to **c:\temp\musicstore\MvcMusicStore.zip**
  * Sitename **Default Web Site**
  * Save the profile and publish with this profile

* Validate this by creating an image and run this locally
  * Copy Docker file, fixAcls.ps1, WebDeploy_2_10_amd64_en-US.msi to **c:\musicstore\temp\\** from the MvcMusic store repo folder: **Provisioning\Docker**
  * Build and run container, call the container mvcmusicstore (--name):
    ```ps1
    docker build . -t mycontainerizedwebsite
    docker run -p 80:80 -d --name mvcmusicstore mycontainerizedwebsite
    docker inspect --format '{{ .NetworkSettings.Networks.nat.IPAddress }}' mvcmusicstore
    ```
* Use docker inspect to get the ip address and test locally

```ps1
    docker inspect --format '{{ .NetworkSettings.Networks.nat.IPAddress }}' mvcmusicstore
```

Congratulations! You are now a Local Docker Hero!!! Achievement unlocked!

## Achievement #3 - Step-by-Step: Set up an Azure Container Registry ##
To setup and Azure Container Registry please follow the instructions [here](https://azure.microsoft.com/en-us/services/container-registry/)

* Enter as name for the registry a name in the following format: ```<VENUENAME><TEAMNAME>```

You can now perform your most craziest dance to celebrate that you are a Azure Container Registry Creation Master. Another Achievement unlocked!

## Achievement #4 - Step-by-Step: Create a build definition for your containerized application ##
To create a build definition for your containerized application please learn about the Docker Build Tasks for VSTS [here](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker)

Proceed by following the steps below
* Change task: Build solution **\*.sln to publish a WebDeploy package on build
 ```
 /p:DeployOnBuild=true;PublishProfile=CustomProfile 
 /p:WebPublishMethod=Package /p:PackageAsSingleFile=true 
 /p:SkipInvalidConfigurations=true /p:PackageLocation="$(build.stagingDirectory)/Provisioning/Docker"
 ```
* Use the Copy Files task to make sure you copy all neccesary files to the ```$(build.stagingDirectory)/Provisioning/Docker``` directory
* Create an ImageName variable set the value to ```<VENUENAME><TEAMNAME>.azurecr.io/mvcmusicstore:$(Build.BuildId)``` (for example ```hilversumteam0001.azurecr.io/mvc-music-store:$(Build.BuildId)```)
* Create a Service EndPoint to your Azure Container Registry
    - Select Docker Registry as Service Endpoint type
    - Enter <VENUENAME><TEAMNAME>ContainerRegistry as connection name
    - Enter the login server name from Achievement 3 in the field Docker Registry value
    - Enter the user name and password from Achievement 3 in the fields Docker ID and Password
    - Enter a value in the email field
* Use the Docker File and the Docker Build and Publish tasks to build and publish an image to your Azure Container Registry
* Use the Copy & Publish Artifacts task to also publish the artifacts in your build

## Achievement #5 - Step-by-Step: Set up a release pipeline that consumes the container from your Azure Container Registry
* Run your image on your local machine with the Docker task
* Use a Manual Intervention task to inspect the IP address of the container and navigate to the container
* Remove the container in the pipeline

## Resources ##
No resources specified
