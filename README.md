# oci-teamspeak-docker

## Prerequisites
Things you need before starting:
* `OCI account`
* `Ansible`

## Project structure
```
oci-terraform
|── docker-compose.yml
|── .mariadb.env
|── .teamspeak3.env
└── ansible-playbooks
     └── install-docker.yaml
     └── install-and-configure-firewalld.yaml
```
## Tasks to accomplish
- The idea of this project is to create a `teamspeak` server in `OCI`.

## How to setup this project locally
- First we should download it with either `git clone` or as `.zip`.
- Then we will use `ansible` playbook `install-docker.yaml` in order to install `docker` and `docker-compose`.
- Then you will have to modify `.mariadb.env` and `.teamspeak3.env` db passwords.

## Networking setup
- First of all we have to add some `ingress rules` in our machine's `VCN`. Your will have to navigate to:
````
Networking >> Virtual cloud networks >> <your_vcn> >> Security List Details >> Ingress Rules
````
- There you will have to allow the following ports: 
````
- 10011 TCP
- 30033 TCP
- 9987 UDP
````   
- Finally in our server we will have to open those ports. I will use `firewalld` which is a wrapper for `iptables`. You can either install it and configure it manually or you can use `install-and-configure-firewalld.yaml` playbook.

## Docker setup
- Now we will copy our `docker-compose.yml` file to our server in `OCI`. Then we will create the containers:
````
$ docker-compose up -d
Creating network "ubuntu_default" with the default driver
Creating ubuntu_mariadb_1    ... done
Creating ubuntu_teamspeak3_1 ... done
````
- Then we will need to access the logs of our `teamspeak` container in order to get the credentials to create the `teamspeak` server.
````
$ docker ps
CONTAINER ID   IMAGE       COMMAND                  CREATED              STATUS PORTS                                                                                         
7049bdbb6611   teamspeak   "entrypoint.sh ts3se…"   About a minute ago   Up About a minute   0.0.0.0:10011->10011/tcp, etc   
39be6e021e69   mariadb     "docker-entrypoint.s…"   About a minute ago   Up About a minute   3306/tcp                                                                                             
$ docker logs 7049bdbb6611                                                                                     
````
- Finally with that info we can create and customize our `teamspeak` server.