# Installation descriptions

## About this document

This document briefly describes developing and installation principles of port activity application software component. Further in depth information of installation and software configurations are not in scope of this document.

## Software components

As described in architecture document software is split into following components:

- API
- Vendor specific agents to help getting timestamps into system 
- Timestamp clustering agent to help clustering timestamps into port calls
- VIS agent that communicates with STM/VIS services
- Web UI and
- Mobile UI

### Setup

Minimum setup to run application application is:

- API
- Timestamp clustering agent and
- Web

Minimum setup to get application running for web and mobile is:

- API
- Timestamp clustering agent and
- Web
- Mobile

Common setup to running application is

- API
- Timestamp clustering agent and
- Web
- Mobile
- VIS agent
- Multiple agents that poll different APIs to get timestamps into system to start generating port calls

### Getting data into system without polling agents

If your data sources can post you directly timestamps then in best case you don't need any agents that poll data. To simulate getting data into system you can do test post timestamp manually yourself:

```shell
curl 'http://localhost:8000/agent/rest/timestamps' \
-XPOST \
-H 'Accept: application/json, text/plain, */*' \
-H 'Content-Type: application/json;charset=utf-8' \
-H 'Authorization: ApiKey xxx' \
-H 'Connection: keep-alive' \
--data-binary '{"imo": "9295347", "vessel_name": "ADVANTAGE PARK", "time_type": "Estimated", "state": "Departure_Vessel_Berth", "time": "2020-10-16T01:00:00.867Z", "payload": {"from_port":"FIHKI", "to_port": "SEGVX", "next_port":"FIRAU"}}'
```

Prior to porting you need to:

- [Generate API key](https://port-activity.github.io/user-manual/#add-api-key) and
- [Setup priority for specific timestamps for that API key](https://port-activity.github.io/user-manual/#api-key-priorities)  

## Common procedure for setting app different software components

### Development environment

For every component follow repository `readme.md` to setup your environment. In most cases you need to

- configure your local environment
- run installation scripts (eg. `yarn install` or `make install`)
- start software (eg. `docker-composer up` or `make run`)

Development environment services are running on localhost and some are running in containers. Eg. default setup is that API is running on `http://localhost:8000` and web UI is running on `http://localhost:3000`. Timestamp clustering agent is running on `http://localhost:5000`. 

Referring from another service running in a container could be tricky since all services are not started with single docker-compose command. Eg. you might need to refer from api to timestamp clustering agent as `http://host.docker.internal:5000/` with mac. On windows and linux environments you need to use localhost or IP directly.

### Deploying components to hosted environment

Most common and convenient way to deploy components is

- build a docker image,
- push that image to some container repository,
- configure environment variables required for service and
- deploy it to environment.

You can orchestrate your deployment with [Kubernetes](https://kubernetes.io) on your cloud provider. You can even take one step further and orchestrate your Kubernetes cluster with [Rancher](https://rancher.com) to avoid as much as possible vendor lock down to a specific cloud provider.

First deployments have been done with Rancher and Google Kubernetes Engine. Most repositories have pipeline scripts that could be used for automate build and deployment process. See `.rancher-pipeline.yaml` and `deployment.yml` files on each repository.

## API

Follow instructions in [https://github.com/port-activity/api](https://github.com/port-activity/api)

When API is up and running it provides backbone for application to run. On development is running on certain port yet on real deployment it should be available with DNS and forwarded with proxy from an url:

- https://my-port-activity-domain.com/api/v1 or
- https://api.my-port-activity-domain.com/v1
  
UIs and other services must be then configured to use this URL. 

API has several dependencies, eg. postgresql and redis, that must be hosted separately. Redis can be non-persistent (yet you might want to run it with persistent data) and postgresql should have long live persistent data since it contains all information.

# Web

Follow instructions in [https://github.com/port-activity/web](https://github.com/port-activity/web).

Web is standard [React](https://reactjs.org) application that uses [Ant Design](https://ant.design) component when ever possible.

React is build in a standard way. Single page application (SPA) is served as static JS bundle.

By default JS bundle could be deployed by building container that has nginx and JS bundle in same docker image. You can also deploy JS more sophisticated way in your cloud provider if you will but most cases your application will not be under so heavy traffic and there is no need to build CDN and such solutions.

This nginx configuration could be used also as proxy for other services when running containers in same namespace. See repository default.conf for setting up nginx as proxy.

# Mobile

Follow instructions in [https://github.com/port-activity/mobile](https://github.com/port-activity/mobile).

Mobile application is build with [React Native](https://reactnative.dev). You need to install (Expo)[https://expo.io] to run application on local environment. The beauty of React Native is that you can build iOS and Android version with same code base without even opening XCode or Android Studio. Drawback is that you need make some compromises and can't use all the fanciest platform specific features.

When building deployment packages you need create account to Expo. If you need team features you have subscribe Expos Priority plan: https://expo.io/developer-services

Uploading application to Apple Store and Google Play store could be automated on some level with Expo. However setting up accounts to both stores is manual process. Also uploading deployment package to stores is more convenient to do manually.

When making releases withing same ExpoSDK version application JS could be deployed directly with Expo. Whenever ExpoSDK is updated full deployment package must be uploaded to stores again.

# Agents

All agents have separate instructions on their repositories. Most agents follow this procedure when polling timestamps:

- Authentication to third party API (if not open)
- Read data from third party API
- Convert data to format that it could be send to Port Activity API
- Post data to Port Activity API with correct API key

If exactly same timestamp data is posted Port Activity API it is ignored since it is duplicate.

Some APIs have just full lists that should be read and converted. Hovever some APIs require that you poll it with proper IMO and other query datas. For these agents there needs to be list of IMOs to poll. In such cases agent should get the list of IMOs from Port Activity API.
