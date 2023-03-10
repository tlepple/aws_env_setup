
# aws_env_setup

---
---

# Build a docker container to execute this build
* The softlink is running against a linux destop.  skip this for non linux systems

```
# create a directory in OS for bind mount:
cd ~
mkdir -p ./Documents/cloud_stuff/docker_stuff

# create a softlink to this directory
sudo ln -s /home/$USER/Documents/cloud_stuff/docker_stuff ~/fishermans_wharf

```

## Create the docker container and mount the directories


```
# Create a docker volume to persist data
docker volume create iceberg_vol1


docker run -it \
  --name aws_iceberg \
  --mount source=iceberg_vol1,target=/app \
  --mount type=bind,source=/home/tlepple/Documents/cloud_stuff/docker_stuff,target=/root/fishermans_wharf \
  centos:7 bash

  
    
```


### Pull the repo

```
#install git
yum install -y git
cd /app    


git clone https://github.com/tlepple/aws_env_setup.git

```


### Edit the demo.properties file for your updates and set AMI and Region you are running in

```
vi /app/aws_env_setup/bin/provider/aws/demo.properties
```

### Sample demo.properties

```
###############################################################################
#  Updates go here:
###############################################################################
OWNER_TAG=tlepple
BIND_MNT_SOURCE="/home/tlepple/Documents/cloud_stuff/docker_stuff"
HOST_PREFIX="icetesting-"

# Below are the instance types for the various services. It's recommended to use
# the defaults, however you can change them below, if needed.
ONE_NODE_INSTANCE=t3.xlarge

#PROJECT_TAG="'""personal development""'"
ENDATE_TAG=permanent

# set the docker bind mount properties
BIND_MNT_TARGET="/fishermans_wharf/"

# Ohio us-east-2
AMI_ID=ami-03a311cadf2d2a6f8
AWS_REGION=us-east-2



###############################################################################
#  No edits required below
###############################################################################

# Username to SSH to instances.
# If you use your own AMI, you may need to change the ssh username as well.
SSH_USERNAME=ubuntu

# set component flags.  helpful in case you need to restart a failed build or use alternate setup
setup_prereqs=true
setup_onenode=true


```


### Export your AWS credentials


```
export AWS_ACCESS_KEY_ID=<your key>
export AWS_SECRET_ACCESS_KEY=<your secret key>
export AWS_DEFAULT_REGION=<aws region you want to run in>

```

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
