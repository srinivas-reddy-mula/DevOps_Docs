## Docker file Writing

#### Docker file Instructions
* FROM
    * TO choose base image for building a __DOCKER IMAGE__

* RUN:
    * To run an inline script 
    * TO run shell Commands

* ADD: 
    * to copy something from an __URL__ or Docker Host
    * to get files or any from __Remote Location__
* COPY:
    * TO copy from __Docker Host__ to __Docker Image__

* EXPOSE:
    * To expose a container to public or private 
* VOLUME: 
    * To get the data __persistency__
    * in Docker Host 
        * we will create __VOLUME__ 
            * Attach  to __Container__
                * whaterver data gets generated in __container__ will get copied into __DockerHost__
    
    * Types:
        * tmpfs:
            * In tmpfs the data will be maintained in __Memory(RAM)__
        * Bind:
            * We will create volume ``` docker volume create <volumename>``` and we will attach to the __Container__ this is called __Binding__
        * Mount:
            * with __--mount__ we will attach Volume to Container
    *  Drivers:
        * Docker supports the following storage drivers:

            * __overlay2__ is the preferred storage driver, for all currently supported Linux distributions, and requires no extra configuration.
            * __aufs(Augmented Union FileSystem)__ is the preferred storage driver for Docker 18.06 and older, when running on Ubuntu 14.04 on kernel 3.13 which has no support for overlay2.
* ENV:
    * To define __Environment Variables__
    * Scope:
        * __ENV__ Variables gets stored in Image so, Container can use those Variables
* ARG: 
    * To define Variables as __Arguments__ like parameters
    * Scope:
        * __*ARG*__ Variables will availble until DockerFile Execution only 

## ENV and ARG Difference
```
ENV variables wiil be stored in DockerImage and Containers can use while running
ARG Variables will be alive until DockerFile Execution time only
```
* CMD:
    * It is the mandadtory instruction
    * it tells the container what is the __*first command*__ to be execute after creation

* ENTRYPOINT:
    * To fix the command starting point
        * there on the commands what you entered is treated as __Arguments__ not as __Main Command__
* HEALTHCHECK:
    * To check whether the container is in __Running__ or __Pending__ or __Stopped__
* WORKDIR:
    * To tell the Docker to work in which __Directory__

#### Docker File Examples
* [spring-pet0clinic]
![preview](https://github.com/srinivas-reddy-mula/Docker/blob/master/spc/Dockerfile)
* [game-of-life](https://github.com/srinivas-reddy-mula/Docker/blob/master/gameoflife/Dockerfile)