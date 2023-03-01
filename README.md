# aws_env_setup

---
---
##  Docker Compose 

####  Create a directory on your host for assets:

```
mkdir -p ./Documents/cloud_stuff/docker_stuff
```
---

####  Create a docker volume that will be attached to our container:

```
# Create a docker volume to persist data
docker volume create iceberg_vol2
```
---

####  Create a docker compose file:

```
cd /home/tlepple/Documents/cloud_stuff/docker_stuff/compose

vi compose.yaml
```
---


#####  File Contents of `compose.yaml`

```
version: "1.0"
services:
  awsbuild:
    image: centos:7
    container_name: aws_iceberg2
    env_file: .env
    volumes:
      - type: volume
        source: iceberg_vol2
        target: /app
      - type: bind
        source: /home/tlepple/Documents/cloud_stuff/docker_stuff
        target: /root/fishermans_wharf
    command: tail -f /dev/null
volumes:
  iceberg_vol2:
    external: true
```
---

####  create a `.env` file for some variables to be called from code:

```
cd /home/tlepple/Documents/cloud_stuff/docker_stuff/compose

vi .env

```
---

#####  File Contents of `.env`

```
# aws keys:
AWS_ACCESS_KEY_ID=<replace with your aws key>
AWS_SECRET_ACCESS_KEY=<replace with your aws secret key>
AWS_DEFAULT_REGION=us-east-2

OWNER_TAG=<your owner tag name>

# change this to point to your bind mount location (wip)
BIND_MNT_SOURCE="/home/tlepple/Documents/cloud_stuff/docker_stuff"

# set the docker bind mount properties
BIND_MNT_TARGET="/fishermans_wharf/"

###############################################################################
#  No edits required below
###############################################################################

HOST_PREFIX="icetesting-"

# Below are the instance types for the various services. It's recommended to use
# the defaults, however you can change them below, if needed.
ONE_NODE_INSTANCE=t3.xlarge

# Ohio us-east-2
AMI_ID=ami-03a311cadf2d2a6f8
AWS_REGION=us-east-2

# Username to SSH to instances. All of the pre-selected AMIs use the 'centos' user.
# If you use your own AMI, you may need to change the ssh username as well.
SSH_USERNAME=ubuntu

# set component flags.  helpful in case you need to restart a failed build or use alternate setup
setup_prereqs=true
setup_onenode=true
#   These variables will persist through a stop and start of a container!
```
---
---


## Build a docker container to execute this build
* The softlink is running against a linux destop.  skip this for non linux systems

```
cd /home/tlepple/Documents/cloud_stuff/docker_stuff/compose

docker compose up -d
```

---

###  Connect to our new docker container:

```
# connect to command line of this container
docker exec -it aws_iceberg bash
```


### Pull the repo for the rest of this setup:

```
#install git
yum install -y git
cd /app    


git clone https://github.com/tlepple/aws_env_setup.git

```
---
---

### Start the build:

```
#  run the setup
cd /app/aws_env_setup

. bin/utilities/setup.sh aws

```


### Sample output of a successful build


```


          ---------------------------------------------------------------------------------------------------------------------------------------------
          ---------------------------------------------------------------------------------------------------------------------------------------------
          |                  	Bind Mount -- SSH Connection String:                                                                                                            
          ---------------------------------------------------------------------------------------------------------------------------------------------
          ---------------------------------------------------------------------------------------------------------------------------------------------
          |       ssh -o StrictHostKeyChecking=no -o IdentitiesOnly=yes -o UserKnownHostsFile=/dev/null -i ~/fishermans_wharf/tlepple-us-east-2-i-022945d15aa273c62-10.0.8.230.pem ubuntu@3.132.118.16          
          ---------------------------------------------------------------------------------------------------------------------------------------------
          ---------------------------------------------------------------------------------------------------------------------------------------------
```

---
---

### Usefull docker commands

```
# list all containers on host
docker ps -a

#  start an existing container
docker container start aws_iceberg

# connect to command line of this container
docker exec -it aws_iceberg bash

#list running container
docker container ls -all

# stop a running container
docker container stop aws_iceberg

# remove a docker container
docker container rm aws_iceberg

# list docker volumes
docker volume ls

# remove a docker volume
docker volume rm iceberg_vol1
```
---
---

# To clean up and terminate all items run

```
cd /app/aws_env_setup

. bin/provider/aws/terminate_everything.sh

```



## Start a stopped Cloud Instance (Currently only works with AWS)
```
cd /app/aws_env_setup
. bin/start_instance.sh

```

## Stop a running Cloud Instance (Currently only works with AWS):
```
cd /app/aws_env_setup
. bin/stop_instance.sh
```
