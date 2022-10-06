# Table of content
1. [Preliminary](#preliminary)
2. [Creating An Andela Application or Microservice](#creating-an-andela-application-or-microservice)
    1. [Steps](#steps)
3. [More information](#more-information)
    1. [Adding endpoints to the API-Gateway](#adding-endpoints-to-the-api-gateway)
    2. [CircleCi](#circleci)
    3. [Kubernetes](#kubernetes)

## PRELIMINARY
When creating an Andela microservice or front end application, it is important to have an understanding of all the technologies used by the Andela microservice [architecture](https://github.com/andela/engineering-playbook/wiki/Architecture). First step is therefore to head over to the architecture page of the engineering playbook to get the high level view of the Andela architecture.

## CREATING AN ANDELA APPLICATION OR MICROSERVICE
### Steps:
1. Create a repository in the Andela Github organisation. This would require privileged access so contact the DevOps team or the Director of Technology.
2. Use starter kit for setup files. Starter kits have necessary files and configurations for setting up a microservice or application. Use the appropriate starter kit for the stack of your choice.
  
  [Go starter kit](https://github.com/andela/micro-go-starter-kit)
    
    [Node.js starter kit](https://github.com/andela/micro-node-starter-kit)
    
    [Python starter kit](https://github.com/andela/micro-python-starter-kit)
    
    [Ruby starter kit](https://github.com/andela/micro-ruby-starter-kit)

3. Add code for your microservice or application. 

  **Microservices**

  - Define endpoints for microservice and add them to the micro-shared repository. - For this task, visit the gRPC endpoints section of the engineering playbook. To get a deeper understanding of gRPC, visit gRPC guide on Github and follow the guide appropriate for your chosen stack. 

  - Expose endpoints to API gateway. The API-Gateway is the middle man for all micro services all requests are done through the Gateway. See the [guide](#adding-endpoints-to-the-api-gateway) on how to expose endpoints.

  **Applications**

  These only require to access the endpoints exposed by the API gateway. No endpoints need to be configured.
4. Create database for your microservice. Andela uses Google Cloud SQL in staging and production.  Before deploying ensure that the database for your microservice is set up on Google Cloud. Talk to the DevOps team to help you set this up.
5. Deploy with CircleCi. 

  - Configure Kubernetes. See [Kubernetes](#kubernetes) section below to create Kubernetes scripts for your micro-service or application.
  - Configure CircleCi:  To get a deep understanding of CircleCi , visit CircleCi documentation. For the Andela microservices and applications, initial CircleCi configuration can be found in the “.circleci” folder of the starter kit as below:
    - [python starter kit configuration.](https://github.com/andela/micro-python-starter-kit/blob/develop/.circleci/config.yml)
        - [node starter kit configuration.](https://github.com/andela/micro-node-starter-kit/blob/develop/.circleci/config.yml)
        - [ruby starter kit configuration.](https://github.com/andela/micro-ruby-starter-kit/blob/develop/.circleci/config.yml)
        - [golang starter kit configuration.](https://github.com/andela/micro-go-starter-kit/blob/develop/.circleci/config.yml)

  The configuration files contains placeholder for the below environment variables and should be replaced with the right values:

      - CONTAINER_NAME
    - DEPLOYMENT
    - IMAGE
    - CONTAINER_NAME_PROD
    - DEPLOYMENT_PROD
    - IMAGE_PROD  

  **NB**: See a description of the steps specified in the Andela [CircleCi](#circleci) configuration files in CircleCi section below. 
6. Add all the other required integrations in code. This includes any monitoring and logging tools that you might require for example bugsnag.
7. Set up DNS. For front end applications, they are accessed through URLs such as “ais.andela.com”. This URLs map to a certain IP address.Talk to the Devops team to set you up with a subdomain for your application.

### More information
#### Adding endpoints to the API-Gateway
* Clone [micro-api-gateway](https://github.com/andela/micro-api-gateway) repository and checkout your own branch.
* Navigate to the “controller” directory.
  `cd controller`
* Add two files to this directory:
  - Your_microservice.go - Contains the functions to be called when endpoints are called.
  - Your_microservice_test.go - Contains tests for your endpoints. 
* Navigate to the file “controller/routes.go” and add all the endpoints from your microservice following convention in use.
* Once API Gateway changes are deployed to staging, confirm the endpoints appear on admin-staging.andela.com under the Activities tab ( Permissions → Activities)

#### CircleCi
The main sections involved in the andela CircleCi are described below:

**Jobs** 

This is a key value map the keys being the job names and the values being the descriptions and everything that comprises the whole job. You can set a build job that describes a build as the as screenshot below:
      
**docker**
    
Since we will be building from docker, specify all the required images for your project and the environment variables required to run the images in docker containers. The required images include:

1. Circle testing image for your stack. Andela has circle test images stored in GCP [container registry](https://goo.gl/T1L2CL). The environment variables set for these images are for both staging production environments. Below are some of the environment variables:
  + ENVIRONMENT(RUBY_ENV|NODE_ENV|PY_ENV) - this is the deployment environment . It is set to “test” by default because the environment here is testing environment.
  + GCLOUD_PROJECT - Refers to the staging project. Set to “microservices-kube” by default.
  + GRPC_HEALTHCHECK_TAG - This tag is required for healthchecks binary. Healthchecks checks your application or micro-service for liveness and readiness. The current version in use is 1.0.1 but it is advised to confirm with the platform team(@platform_team) to get the latest version.
  + PUBSUB_EMULATOR_HOST - This is the url to the pubsub emulator. Set to “localhost:8085” by default.
  + PROJECT_NAME - The name of the staging project on GCP. set to “microservices-kube” by default.
  + PROJECT_NAME_PROD- This is the name of the production project on Google cloud. It is set to “andela-kube” by default.
  + CLUSTER_NAME_PROD  This is the name of the production cluster on Google CLoud container engine.  The default value is “andela-prod”.
  + CLUSTER_NAME - Name of cluster in the staging (microservices-kube) Andela project. Set to “microservices-kube” by default.
  + DATABASE_URL - If the product in creation is a microservice and it requires a DB, set this to the URL of the database. The value used here is “postgres://ubuntu@localhost:5432/circle_test”.
  + IMAGE_NAME - Name of the image of your project.  The name of the microservice or application is used as the image for easy reference.
  + CONTAINER_NAME - container name set . Just like the image name, the name of the microservice of application is used as the container name for ease of reference.
  + DEPLOYMENT_PROD - set to the name of the Kubernetes deployment of your project. This is set to the name of your microservice or application for ease of reference.
  + DOCKER_PROJECT_NAME - A repository containing the shared dockerfile.
2. Database image eg postgres image - If the project in creation is a microservices that requires a database, then the database container is specified. The database image could require the following environment variables:
  + POSTGRES_USER - The postgres user who owns the database.
  + POSTGRES_DB - The name of the database to be used by the microservice.
  + POSTGRES_PASSWORD - The password if any to access the database.
3. Others include a pubsub emulator image to emulate message sourcing among the micro-services.  The image used for this is: “ - image: knarz/pubsub-emulator”

##### Other build steps:
1. Update the shared submodule. - All Andela application and microservices use the micro-shared repository as a submodule in their own repository. When the project repository is pulled, the shared submodule is initialised and updated using:
  git submodule sync && git submodule update --init
2. Install dependencies. - Install all the dependencies in your dependency file for your stack using the appropriate command. Ie : 
  yarn add - node
  Pip install -r requirements.txt - python
  Bundle install - For ruby
3. Run tests. - supply the appropriate commands to run tests in your stack
4. Report coverage - In Andela, codeclimate is used to report test coverage. In your stack, supply the command to push the circleCi results to code climate.
5. Deploy code - This is done in the deploy section of circle config file and when the previous stages exit with no error. The following steps are followed during deployment:
  + Your code is pulled from your branch (develop during staging and master in production) and used to create an image with the shared docker source. The shared docker source is specified as an environment variable (DOCKER_PROJECT_NAME).
  + Your image is pushed to your image repository in google container registry. This action requires your project name specified as the environment variable PROJECT_NAME and the name of your  image repository is specified as an environment variable (IMAGE).
  + Apply the Kubernetes deployment. Google cloud offers a container engine where all the Kubernetes orchestration happens. To deploy in Kubernetes, the following need to be observed.
    - Ensure that a cluster has been set up. In Andela, there are two clusters. One in the production andela-kube project and a second one for the staging microservices-kube project. The cluster name is specified as an environment variable in CircleCI as CLUSTER_NAME.
    - Ensure that there is an image for your project which should now be in docker registry.
    - Create a Kubernetes deployment script for your product as described in following section.
    - Apply the created file using kubectl deployment application command.
    - Apply the created corresponding service for your project.

#### KUBERNETES
 Andela uses Kubernetes for deployments. Since the location of the hosted microservices changes dynamically due to the concept of load balancing, Kubernetes runs a proxy on each host of the cluster. The proxy offers server-side discovery load-balancer. To make a request to a certain micro-service, a request is sent to the proxy’s host Ip address and proxy used service discovery to get to IP address of the exposed corresponding Kubernetes services’s port . Zookeeper is also used as a service registry in the Andela context and it keeps metadata concerning the services including their IP addresses.

Kubernetes deployments and services are configured using yaml configuration files. Stored in the micro-deployment-scripts. Follow [this guide](https://github.com/andela/DevOps-Misc-Scripts/wiki/How-to-create-deployment-scripts-for-Andela-products) to create deployment scripts for your microservice of application.
