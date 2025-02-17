# Dockerfile for WSO2 Open Banking Business Intelligence Accelerator Module
This section defines the step-by-step instructions to build an [Alpine](https://hub.docker.com/_/alpine/) Linux based Docker image for WSO2 Open Banking Business Intelligence.

## Prerequisites

* [Docker](https://www.docker.com/get-docker) v20.10.10 or above
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) client
* WSO2 Open Banking Business Intelligence Accelerator Module pack downloaded through [WSO2 Updates 2.0](https://ob.docs.wso2.com/en/latest/install-and-setup/setting-up-servers/)
* Keystores directory of wso2 server certs of WSO2 Open Banking Docker Images. (https://github.com/wso2/docker-open-banking/raw/v3.0.0.10/keystores)

  + Host the downloaded artifacts locally or on a remote location.
  > The hosted locations of artifacts will be passed as the build arguments when building the Docker image.<br>
  > 1. **WSO2_OB_ACCELERATOR_DIST_URL** - Accelerator location
  > 2. **WSO2_OB_KEYSTORES_URL** - location of keystores folder of wso2 server certs


## How to build an image and run

##### 1. Checkout this repository into your local machine using the following Git client command.

```
git clone https://github.com/wso2/docker-open-banking.git
```

> The local copy of the `dockerfiles/alpine/obbi` directory will be referred to as `OBBI_DOCKERFILE_HOME` from this point onwards.

##### 2. Build the Docker image.

- Navigate to `<OBBI_DOCKERFILE_HOME>` directory. <br>
  Execute `docker build` command as shown below.
  + `docker build --build-arg BASE_PRODUCT_VERSION=<APIM BASE PRODUCT VERSION> --build-arg WSO2_OB_ACCELERATOR_DIST_URL=<URL_OF_THE_HOSTED_LOCATION/FILENAME> --build-arg WSO2_OB_KEYSTORES_URL=<URL_OF_THE_HOSTED_LOCATION/FOLDER_NAME> -t wso2-obbi:3.0.0-alpine .` <br>
  > eg:- **Hosted locally**: `docker build --build-arg BASE_PRODUCT_VERSION=4.2.0 --build-arg WSO2_OB_ACCELERATOR_DIST_URL=http://localhost:8000/wso2-obbi-accelerator-3.0.0.tar.gz --build-arg WSO2_OB_KEYSTORES_URL=http://localhost:8000/keystores -t wso2-obbi:3.0.0-alpine .` <br><br>
  > eg:- **Hosted remotely**: `docker build --build-arg BASE_PRODUCT_VERSION=4.2.0 --build-arg WSO2_OB_ACCELERATOR_DIST_URL=http://<public_ip:port>/wso2-obbi-accelerator-3.0.0.tar.gz --build-arg WSO2_OB_KEYSTORES_URL=http://<public_ip:port>/keystores -t wso2-obbi:3.0.0-alpine .`

##### 3. Running the Docker image.

- `docker run -it -p 9712:9712 -p 9612:9612 -p 7712:7712 -p 7612:7612 -p 9092:9092 -p 9444:9444 wso2-obbi:3.0.0-alpine`
       
## How to update configurations

Configurations would lie on the Docker host machine and they can be volume mounted to the container. <br>
As an example, steps required to change the port offset using `deployment.yaml` is as follows:

- Make sure that API Manager analytics is enabled in `<APIM_HOME>/repository/conf/deployment.toml` as follows.
```
[apim.analytics]
enable = true
```

- Go to `<APIM_HOME>/repository/conf/deployment.toml` and `<IS_HOME>/repository/conf/deployment.toml` to enable open banking data publishing as follows:

```
[open_banking.data_publishing]
enable = true
```

##### 1. Stop the OBBI container if it's already running.

In WSO2 Open Banking BI 3.0.0 product distribution, `deployment.yaml` configuration file <br>
can be found at `<DISTRIBUTION_HOME>/conf/server`. Copy the file to some suitable location of the host machine, <br>
referred to as `<SOURCE_CONFIGS>/deployment.yaml` and change the offset value under ports to 1.

##### 2. Grant read permission to `other` users for `<SOURCE_CONFIGS>/deployment.yaml`.

```
chmod o+r <SOURCE_CONFIGS>/deployment.yaml
```

##### 3. Run the image by mounting the file to container as follows:

```
docker run \
-p 9712:9712 -p 9612:9612 -p 7712:7712 -p 7612:7612 -p 9092:9092 -p 9444:9444 \
--volume <SOURCE_CONFIGS>/deployment.yaml:<TARGET_CONFIGS>/deployment.yaml \
wso2-obbi:3.0.0-alpine
```

> In here, <TARGET_CONFIGS> refers to /home/wso2carbon/wso2si-4.0.0/conf/server folder of the container.

## Docker command usage references

* [Docker build command reference](https://docs.docker.com/engine/reference/commandline/build/)
* [Docker run command reference](https://docs.docker.com/engine/reference/run/)
* [Dockerfile reference](https://docs.docker.com/engine/reference/builder/)
