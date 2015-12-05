#CSC 591/791 - DevOps Special Milestone: Doctor Monkey and Load Balancer

## Introduction

For the Special Milestone, we have extended on Milestone 3 and created a Doctor Monkey and a new load balancer. The idea is to be able to do load balancing when a service is available on different production hosts i.e. routing requests to servers based on minimum CPU utilization. Also, we have created a monkey that would detect if a host is currently overloaded and thereby restart the host and all services.

The architecture is as follows:
<ADD IMAGE>

We have created five Digital Ocean Droplets that would act as different Production servers. We have created different droplets for Proxy Server and Global Redis Store as well.

## Tasks

### Automatic Configuration of Production Environment
* The code related to spinning up a droplet acting as the Production Server, creating inventory entry, production server configuration yml read by ansible playbook can be found in the [Prod](https://github.com/amittal91/DevOps-Project-SpecialMilestone/tree/master/Prod) directory
* A file namely 'digitalocean_config.json' would have key-value pairs for token, ssh_key and keypath to the private key
The file [prod_configuration.yml](https://github.com/amittal91/DevOps-Project-SpecialMilestone/blob/master/Prod/prod_configuration.yml) contains all the pre-requisites like nodejs, npm, forever, python, stress, mailutils etc which would be installed as a part of configuration management on the Prod Server
* Our script namely [setup_prod.sh](https://github.com/amittal91/DevOps-Project-SpecialMilestone/blob/master/setup_prod.sh) would be executed for this task. This would create a Prod server and through ansible playbook command configure all dependencies/pre-requisites on the remote server

### Doctor Monkey and Load Balancer
* After configuring production hosts automatically, our application gets deployed on each host with a doctor monkey running in the background
* The doctor monkey checks for CPU Utilization at fixed intervals of time and updates the global redis store with the CPU Utilization corresponding to it's host
* The Proxy Server uses this value to route the request to the production server with the least CPU Utilization
* If the CPU Utilization is above thresholds for three monitoring cycles, an email is sent regarding system reboot, the redis store is updated by removing this host entry and the doctor monkey reboots the host
* When the system is down, the proxy does not route request to this server. It automatically routes request to another server which has least utilization at that instant
* After reboot, all the services are restarted automatically and the host entry and utilization is added back to the redis store so that proxy may choose from available hosts

### Screencast

