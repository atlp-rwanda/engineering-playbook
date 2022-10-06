# 12 Factor Application

Today, a lot of applications are services deployed in the cloud, on infrastructure of cloud providers such as Amazon AWS, Google Compute Engine, DigitalOcean, Rackspace, etc.

12 factor methodology is the result of their observations. As the name states, it presents 12 principles that will help application to be cloud ready, horizontally scalable, and portable.

In this write-up, we will follow each one of the 12 factor and see how we build microservices that are 12 factor compliant. I will be using `application` to mean `authorization microservice` throughout this writeup. However, we are applying the principles across all our microservices and frontend apps.

## 1 - Codebase

**one application/microservice <=> one codebase**

Multiple apps sharing the same code is a violation of twelve-factor. The solution here is to factor shared code into libraries which can be included through the dependency manager. In our case, we used a [shared repo](https://github.com/andela/micro-shared) and git submodules is used to include shared code.

One codebase used for several deployments of the application
* development
* staging
* production

### What does that mean for our application?

We use Git versioning system to handle our source code and hence, the codebase is the same across all deploys. For example, a developer has some commits not yet deployed to staging; staging has some commits not yet deployed to production. But they all share the same codebase, thus making them identifiable as different deploys of the same app.

## 2 - Dependencies

Application's dependencies must be declared and isolated

### What does that mean for our application?

Declaration are done in `package.json` file.

Authorization service package.json file looks like the following:

```
{
  "name": "micro-authorization-service",
  "version": "1.0.0",
  "description": "",
  "main": "server.js",
  "scripts": {
    "start": "node_modules/.bin/sequelize db:migrate && node index.js",
    "test": "export NODE_ENV=test; node_modules/.bin/sequelize db:migrate && ./coverage.sh"
  },
  "repository": {
    "type": "git",
    "url": "git+https://github.com/andela/micro-authorization-service.git"
  },
  "author": "",
  "license": "ISC",
  "bugs": {
    "url": "https://github.com/andela/micro-authorization-service/issues"
  },
  "homepage": "https://github.com/andela/micro-authorization-service#readme",
  "dependencies": {
    "bluebird": "^3.4.0",
    "camelcase-keys": "^4.0.0",
    "dotenv": "^2.0.0",
    "grpc": "^1.0.0",
    "konfig": "^0.2.1",
    "lodash": "^4.11.1",
    "micro-health-check": "0.0.0",
    "moment": "^2.13.0",
    "pg": "^6.0.3",
    "sequelize": "^3.23.4",
    "sequelize-cli": "^2.4.0",
    "should": "^8.3.1",
    "winston": "^2.2.0"
  },
  "devDependencies": {
    "eslint": "^3.8.1",
    "eslint-config-airbnb": "^10.0.1",
    "eslint-plugin-import": "^1.16.0",
    "eslint-plugin-jsx-a11y": "^2.2.3",
    "eslint-plugin-react": "^6.4.1",
    "gulp": "^3.9.1",
    "gulp-coveralls": "^0.1.4",
    "gulp-exit": "0.0.2",
    "gulp-istanbul": "^1.0.0",
    "gulp-mocha": "3.0.0",
    "gulp-nodemon": "^2.0.6",
    "gulp-shell": "^0.5.2",
    "istanbul-combine": "^0.3.0",
    "mock-require": "^1.3.0",
    "nock": "^8.0.0",
    "q": "^1.4.1",
    "run-sequence": "^1.2.2",
    "should": "^8.3.1"
  }
}
```

Dependencies are isolated within `node-modules` folder where all the [node](https://www.npmjs.com/) packages or libraries are compiled and installed.

## 3 - Configuration

Configuration (credentials, database connection string, etc) should be stored in the environment.

### What does that mean for our application ?

In `database.js`, we define the `postgres`connection and use DATABASE_URL environment variable to pass the `postgres` connection string. We use [dotenv](https://yarnpkg.com/en/package/dotenv) yarn package to easily manage environment variables for local development.

```
const production = {
  url: process.env.DATABASE_URL,
  dialect: 'postgres',
};

const config = {
  development,
  production,
  ---
};

module.exports = config;
```

In `models/index.js`, we make sure the `postgres` connection defined above is the one used.

```
const config = require('../database')[env];
const sequelize = new Sequelize(config.url, Object.assign({}, options));
```

Those changes enable to provide a different _DATABASE_URL_ very easily as it's defined in the environment.

## 4 - External services

Handle external services as external resources of the application.

Examples:
* database
* Google PubSub
* redis
* ...

This ensure the application is loosely coupled with the services so it can easily switch provider or instance if needed

### What does that mean for our application?

For instance, for the `authorization service`, we are an external backing services namely postgres database. This loose coupling is  done by the `DATABASE_URL` used to pass the connection string.

If something wrong happens with our instance of postgres, we can easily switch to a new instance, providing a new `DATABASE_URL` environment variable and restart the application.

## 5 - Build / Release / Run

Build / Release and Run phases must be kept separated

![Build/Release/Run](https://dl.dropboxusercontent.com/u/2330187/docker/labs/12factor/build_release_run.png)

A release is deployed on the execution environment and must be immutable.

### What does that mean for our application ?

We use Docker in the whole development pipeline and with the help of a  Dockerfile, we defined the build phase (during which the dependencies are compiled in _node-modules_ folder)

```Dockerfile
FROM node:6-alpine

RUN mkdir -p /usr/src/app

# Change working directory
WORKDIR /usr/src/app

# Add dependencies package
ADD package.json /usr/src/app/

# Install dependencies
RUN yarn install --production

# Copy source code
ADD . /usr/src/app/

# Set port binding for service.
ENV PORT 50050
# Expose API port to the outside
EXPOSE 50050

# Launch application
ENTRYPOINT ["yarn", "start"]
```

Let's build our application `$ docker build -t us.gcr.io/microservices-kube/authorization:0.0.0 .`

And verify the resulting image is in the list of available images

```
$ docker images
REPOSITORY                                       TAG           IMAGE ID           CREATED             SIZE
us.gcr.io/microservices-kube/authorization      0.0.0          f35464cf4b0b       2 seconds ago       157 MB
```

Now the image (build) is available, execution environment must be injected to create a release.
We inject execution environment via kubernetes deployment scripts. Let's see what the execution scripts for authorization service looks like.

```yaml
// 2_kube_deployment.yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: authorization
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: authorization
        tier: microservice
        language: nodejs
    spec:
      containers:
      - name: authorization
        image: us.gcr.io/microservices-kube/authorization:0.0.0
        ports:
        - containerPort: 50050
        env:
          - name: NODE_ENV
            valueFrom:
              secretKeyRef:
                name: setup-secrets
                key: environment
          # database setup
          - name: POSTGRES_HOST
            valueFrom:
              secretKeyRef:
                name: setup-secrets
                key: postgres-host
          - name: POSTGRES_USER
            valueFrom:
              secretKeyRef:
                name: setup-secrets
                key: postgres-user
          - name: POSTGRES_PASSWORD
            valueFrom:
              secretKeyRef:
                name: setup-secrets
                key: postgres-password
          - name: POSTGRES_DB
            value: authorization
          - name: DATABASE_URL
            value: "postgres://$(POSTGRES_USER):$(POSTGRES_PASSWORD)@$(POSTGRES_HOST):5432/$(POSTGRES_DB)"
```
Notice that some env variables where injected from a secret config in the running kubernetes cluster.

This file defines a release as it considers a given build and inject the execution environment.

The run phase can be done manually or via our delivery pipeline by executing `kubectl apply -f 2_kube_deployment.yaml` or when we patch an existing deployment with new release using `kubectl patch` command.

## 6 - Processes

An application is made up of several processes.

Each process must be stateless and must not have local storage (sessions, ...).

This is required
* for scalability
* fault tolerance (crashes, ...)

The data that need to be persisted, must be saved in a stateful resources (Database, shared filesystem, ...)

Eg: sessions can easily be saved in a Redis kv store

Note: Sticky session violate 12 factor.

### What does that mean for our application ?

All our microservices are stateless and hence, no need to worry about storing states or bothering with sessions.

## 7 - Port binding

This factor is related to the exposition of the application to the outside

The host has the responsibility to route the request to the correct application through port mapping.

### What does that mean for our application ?
To be compliant with 12 factor, authorization service exports HTTP as a service by binding to a port, and listening to requests coming in on that port.

With the help of docker, we bound the service on a port as defined by `ENV PORT 50050`. The **app** container exposes port 50050 internally and a kubernetes service maps it against 50050.

```
apiVersion: v1
kind: Service
metadata:
  name: authorization-svc
  labels:
    app: authorization-svc
    tier: microservice
    language: nodejs
spec:
  ports:
  - port: 50050
    protocol: TCP
  selector:
    app: authorization
```

If several instances of the app services needs to be deployed, the configuration above cannot be used as a given port on the host cannot map several ports in the containers.

In this case, we can use a load balancer to which the host will map a port. The load balancer will then be in charge to balance the traffic on the different instances of the services. 

## 8 - Concurrency

Horizontal scalability with the processes model.

The app can be seen as a set of processes of different types
* web server
* worker
* cron

Each process needs to be able to scale horizontally, it can have it's own internal multiplexing.

### What does that mean for our application ?

The authorization service only have one type of process (http server), since it's stateless, we can easily scale horizontally by using kubernetes horizontal scaling feature(`kubectl scale deployment authorization --replicas 3`). We can go as far as setting up autoscalers that enables the service to scale horizontally based on applied loads.

## 9 - Disposability

Each process of an application must be disposable.

* it must have a quick startup
  * ease the horizontal scalability
* it must ensure a clean shutdown
  * stop listening on the port
  * finish to handle the current request
  * usage of a queueing system for long lasting (worker type) process

### What does that mean for our application?

Our application exposes HTTP endPoints that are easy and quick to handle. If we were to have some long lasting worker processes, we use Google PubSub to handle it (services that are purely worker processes include email notification and slack notification service).

Google PubSub stores events processed by each worker. When a worker is restarted, it can provide an index indicating at which point in time it needs to restart the event handling. Doing so no events are lost.

## 10 - Dev / Prod parity

The different environments must be as close as possible.

Docker is very good at reducing the gap as the same services can be deployed on the developer machine as they could on any Docker Hosts.

A lot of external services are available on the Docker Hub and can be used in an existing application. Using those components enables a developer to use Postgres in development instead of SQLite or other lighter alternative. This reduces the risk of small differences that could show up later, when the app is on production.

This factor shows an orientation toward continuous deployment, where development can go from dev to production in a very short timeframe, thus avoiding the big bang effect at each release.


### What does that mean for our application ?

With minikube, we can setup kubernetes on our local machine and run the same production setup locally. So, kubernetes and docker really shines in this area.

The exact same set of microservices can run on each environment in exactly the same way.

## 11 - Logs

Logs need to be handle as a timeseries of textual events

The application should not handle or save logs locally but must write them in stdout / stderr.

A lot of services offer a centralized log management ([Elastic Stack / ELK](https://www.elastic.co/products) , [Splunk](http://splunk.com), [Google Cloud Logging](https://cloud.google.com/logging), ...), and most of them are very easily integrated with Docker.

### What does that mean for our application ?

Since we are using google container engine, logs from all components in the kubernetes cluster are aggregated by GCP's [Stackdriver Logging](https://console.cloud.google.com/logs/viewer).

See an example screenshot below:

![Stackdriver Logging](https://user-images.githubusercontent.com/13568167/45154806-7d22f100-b1e1-11e8-8a0a-e8405e7d8602.png)

## 12 - Admin processes

Admin process should be seen as a one-off process (opposed to long running processes that make up an application).

Usually used for maintenance task, though a REPL, admin process must be executed on the same release (codebase + configuration) than the application.

### What does that mean for our application ?

Authorization service runs `sequelize db:migrate` each time it starts up as defined by `yarn start` task. We can also ssh directly into the running container using `kubectl exec -it POD_NAME sh` or run the command directly using `kubectl exec POD_NAME -- COMMAND_NAME`.
