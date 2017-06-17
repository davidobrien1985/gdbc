# DevOps Challenge \#1 #
In this challenge, you will move the current monolith application towards the cloud. The local SQL Server should be replaced with SQL Azure, and the website should be converted to a PaaS Web App on Azure. The deployment of this website should be done with an automated build and release pipeline.

## Content ##
* [Pre-requisites](#pre-requisites)
* [Getting started](#getting-started)
* [Achievements](#achievements)
* [Bonus Goals](#bonus-goals)
* [Resources](#resources)

## Pre-requisites ##
* Build and release pipeline per team
* Visual Studio 2017 Enterprise
* Azure Subscription
* Team Project for venue with git repo per challenge team
* VSTS account per participant
* Participants need administrator role on the Default Agent Pool (to be able to install a local build agent)
* Participants need administrator rights to add new service end-points in VSTS

## Getting started ##
To work with this challenge you have to create a repo for the application code.

Clone the started code from [https://github.com/GlobalDevOpsBootcamp/challenge1](https://github.com/GlobalDevOpsBootcamp/challenge1) to your VSTS project.

Follow the instructions on this blog [Migrate a complete Git Repository from TFS to VSTS](https://roadtoalm.com/2017/02/10/migrate-a-complete-git-repository-from-tfs-to-vsts/) to clone the challenge from github and add the source to VSTS by using the `git --mirror` statement

* Clone the Git repo with everything in it
```
git clone  https://github.com/GlobalDevOpsBootcamp/challenge1 --mirror
```

* Rename github and Add the new remote
```
git remote rename origin github
git remote add origin https://globaldevopsbootcamp.visualstudio.com/_git/gitreponame
```
* Push all the changes
```
git push origin --mirror
```


## Achievements ##
| # | Achievement   | Maximum score |
|---|---------------|---------------|
|1| Configure a build agent | 25 |
|2| Create the server environment | 25 |
|3| Update the application to use the Azure SQL database | 25 |
|4| Set up a build pipeline that builds the web application in to a MSDeploy package | 50 |
|5| Set up a release pipeline that publishes the application to production | 50 |
|6| Deploy a change in the website with Continuous Deployment | 25 |

## Bonus goals ##
| # | Bonus goal   | Maximum score |
|---|---------------|---------------|
|1| Add an extra stage to the release pipeline that uses a deployment slot | 50 |
|2| Create a backlog with work and a task board with tasks | 25 |
|3| Set up some usage counters with Application Insights in the website | 50 |
|4| Create a feature toggle that "reveals" new functionality | 50 |
|5| Use configuration to inject the connection string for the application during deployment | 50 |
|6| Use an ARM template to configure and deploy the website and database in Azure | 100 |
|7| Use approvers to create a controlled deployment pipeline | 25 |

## Achievement \#1 - Configure a build agent ##
For this challenge we will use local build servers. In this step you will setup a local agent on your own machine to simulate an internal build server running against VSTS.

* Create a build pool, name it by your assigned user id.
* Install and configure a build agent
* Make sure that your build and release definitions point to this build agent by using capabilities and demands (!)

[Learn about configuring a Build Agent](https://www.visualstudio.com/en-us/docs/build/concepts/agents/agents#install-and-connect-to-team-services-and-tfs-2017)

## Achievement \#2 - Create the server environment ##
Now we need a place to host our application in Azure. We want to use the PaaS services in Azure to host a website and a database.

* Use the Azure Portal to create a new resource group for the environment, name the resource group the unique team name you have been assigned.
* Add a web app to the resource group, name the web app the unique team name you have been assigned. Choose to create a new app service plan.
* Add an Azure SQL database. Name the database **MusicStore** and use a **Basic** pricing tier.
* Do not forget to add your clientIP to be able to access the database

## Achievement \#3 - Update the application to use the Azure SQL database ##
The application uses Entity Framework to manage the database. This means that if the database does not exist it will be created. So with that we don't need to manage the database schema or create a database, all we need to do is ensure that the application has an updated connection string that points to the Azure SQL database.

* Find the connection string to the Azure database in the Azure portal.
* Update the Music Store **web.release.config** file.

Here's an example of a connection string:

    ```
    <add name="MusicStoreEntities" connectionString=<server_name>;Initial Catalog=<database_name>;Integrated Security=False;User ID=<user_id>;Password=<password>;Connect Timeout=60;Encrypt=False;TrustServerCertificate=True;ApplicationIntent=ReadWrite;MultiSubnetFailover=False" providerName="System.Data.SqlClient" />
    ```

## Achievement \#4 - Configure a VSTS Build process ##
In this step you will create a new build definition in VSTS that

* Use a CI trigger to continuously validate the code
* Use WebDeploy to create a deployment artifact
* Publish the WebDeploy package as a build artifact
* Make sure that your build and release definitions point to the agent queue configured earlier.
* Disable the test step since the tests may fail (these are integration tests you can try to use later).

As a help here's an MS Build argument that creates a build package:

```
/p:DeployOnBuild=true /p:WebPublishMethod=Package /p:PackageAsSingleFile=true /p:SkipInvalidConfigurations=true /p:PackageLocation="$(build.stagingDirectory)"
```

## Achievement \#5 - Configure a VSTS Release pipeline ##
With the build in place you can now setup a release definition in VSTS to manage the release process.

Here are some of the things the release process for Music store to Azure PaaS should be able to do:

* Use a CD trigger to continuously deploy the application when a new build comes out
* Use environments to model the infrastructure and the release process
* Use WebDeploy to publish the application to the Azure environment using the "[Deploy Azure App Service](https://aka.ms/azurermwebdeployreadme)" build task.
* Make sure that your build and release definitions point to the agent queue configured earlier.

## Achievement \#6 - Deploy the application ##
Now you should be ready to deploy your application using CI/CD for the first time. Make a small code change, commit it and look at the change go through the build and deploy process.

## Resources ##
Additional resources can be found here
* [Azure SQL Database](https://docs.microsoft.com/en-us/azure/sql-database/)
* [VSTS CI and CD overview](https://www.visualstudio.com/en-us/docs/build/overview)