# LAB
## Docker Swarm
*Lab Objectives* 

This lab will focus on configuring the Docker Engine to run in Swarm Mode. The lab will have some demonstrations on how to use Docker Swarm.   

Lab Structure - Overview
1.	Configure Docker Engine to run as a Manager in Swarm Mode
2.	Configure Docker Engine to run as a Worker in Swarm Mode
3.	Deploy a basic NGINX service
4.	Scale a basic NGINX service
 

### 1. Deploy a Docker Composition on Swarm
Step by Step Guide
1.	Locate the IP address of the Master machine in lab folder.

2.	If on a Mac, or using Linux:  
In a command line, enter  
`ssh -i </Users/…/>docker.pem ubuntu@<IP>`
The .pem file will be provided by the instructor for this lab. This command will connect the console to the Docker machine.  

*If using Windows: Open Putty and connect to the session you saved earlier.*
 

3.	Look at the composition of the Docker Engine. Note: Swarm: Inactive  
`docker info`  
As mentioned in the Presentations, all Docker Engines running on 17.03CE and above can run in Swarm Mode. Unlike previous releases of Docker, the Docker Engine is inherently built to run as part of a Docker Swarm Cluster. Note the section Swarm in the command output, e.g. 
    ```
    Swarm: inactive
    ```

4.	In the home directory in the command line, enter the following command, which enables Swarm Mode.   
`docker swarm init`  
    *Note: if the VM has multiple IPs you will get an error about not being able to determine what IP to bind to. Find the public IP and use --advertise-addr to configure it and try again*  
    Output  
    ```
    Swarm initialized: current node (cfly85ocjowrh1egmgqb6mn0w) is now a manager.
    ```
    We will not be adding a worker yet, but to do so we can use the output from the `swarm init` command. It will look similar to this but the token will be unique. Be sure to copy and save the output of the command so you will have it for later:  
    `docker swarm join \`  
    `--token SWMTKN-1-0qmd0ocza1hau359tjtlhvqyrboe3dtxgc196xy95 \`  
    `10.0.0.20:2377`  

  5.	Validate that Docker Swarm Mode is enabled.  
`docker info`  
This will show a change to the value of Swarm. Note: Nodes and Manager section, as well as the "Is Manager: true" field.  
    ```
    Swarm: active
    NodeID: cfly85ocjowrh1egmgqb6mn0w
    Is Manager: true
    ClusterID: 7r812spc4dmlmau1seoroe0q8
    Managers: 1
    Nodes: 1
    ```

6.	List the currently registered nodes in Docker Swarm.  
`docker node ls`  
This will show a change to the value of Swarm. Note: Nodes and Manager section, as well as the "Is Manager: true" field.  
    ```
    ID        HOSTNAME  STATUS  AVAILABILITY     MANAGER STATUS
    cfly85 *  node-0    Ready   Active           Leader 
    ```

7.	Show the output of the Docker Networks.  
`docker network ls`  
NOTE: The creation of the new ingress overlay network with a scope of swarm.  
    ```
    NETWORK ID          NAME                DRIVER              SCOPE
    9f1f2713bf34        bridge              bridge              local
    21c6fb29ea41        docker_gwbridge     bridge              local
    fea724c66379        host                host                local
    xktxyfejvicl        ingress             overlay             swarm
    babe87c79a79        none                null                local
    ```

8.  If you ever lose the join token you can retrieve it with::  
`docker swarm join-token worker`  
NOTE: This is the token one would use to join a worker to this Docker Swarm.   
To add a worker to this swarm, run the command you copied previously, it will look similar to:  

    `docker swarm join \`  
    `--token SWMTKN-1-0qmd0ocza1hau359tjtlhvqyrboe3dtxgc196xy9 \`  
    `10.0.0.20:2377`  

9.	Now inspect the configuration of the  Swarm Manager.  
Run this command first:  
`docker node inspect <MASTER NODE>`  
This command will output a JSON file. JSON can be hard to read at times, and the --pretty flag will generate more human-readable output.  
`docker node inspect --pretty <MASTER NODE>`  
NOTE: This contains configuration, including labels of the Docker Swarm Node. Attention should be given to Status and Raft Status when looking at a Manager Node. 
    ```
    ID:			cfly85ocjowrh1egmgqb6mn0w
    Hostname:		node-0
    Joined at:		2017-05-14 05:45:10.523400514 +0000 utc
    Status:
    State:			Ready
    Availability:		Active
    Address:		127.0.0.1
    Manager Status:
    Address:		10.0.0.20:2377
    Raft Status:		Reachable
    Leader:		Yes
    Platform:
    Operating System:	linux
    Architecture:		x86_64
    Resources:
    CPUs:			2
    Memory:		1.953 GiB
    Plugins:
    Network:		bridge, host, macvlan, null, overlay
    Volume:		local
    Engine Version:		17.03.1-ced
    ```

10.	To help visualize activity in the Swarm cluster, start a Swarm service with the visualizer sample application:  
`docker service create \`  
  `--name=viz \`  
  `--publish=8080:8080/tcp \`  
  `--constraint=node.role==manager \`  
  `--mount=type=bind,src=/var/run/docker.sock,dst=/var/run/docker.sock \`  
  `dockersamples/visualizer`

11.	Once the service is running, use a browser to connect to Master node's IP on port 8080 to bring up the visualizer. Keep this page up to check on the status of the cluster after subsequent actions.

12.	Log into member1 and use the worker join-token to join the node to the Docker Swarm as a worker node.

13.	Log back into Master, and list the currently registered nodes in Docker Swarm.  
`docker node ls`  
Note: * shows the node you are currently logged into.   
    ```
    ID        HOSTNAME  STATUS  AVAILABILITY     MANAGER STATUS
    cfly85 *  node-0    Ready   Active           Leader 
    byd8sd    node-1    Ready   Active
    ```

14.	Log into member2 and use the worker join-token to join the node to the Docker Swarm as a worker.

15.	Log back into Master, and list the currently registered Swarm nodes again, e.g.
    ```
    ID        HOSTNAME  STATUS  AVAILABILITY     MANAGER STATUS
    cfly85 *  node-0    Ready   Active           Leader 
    byd8sd    node-1    Ready   Active
    de83om    node-2    Ready   Active
    ```

16.	Create a basic Docker Swarm Service that runs a simple web container on port 8081:  
`docker service create --name nginx --publish 8081:80 satoms/hello-world:swarm`  
The output of the command will be the ID of the new Swarm Service.
    ```
    n609tobgp7p93sbuo5x6rp1sd
    ```

17.	Run the curl command against: http://0.0.0.0:8081  
`curl http://0.0.0.0:8081`  

This will output a greeting web page that outputs the hostname of the container. Can you open this web page in a browser from your local machine? Do you need to connect to a particular node in your lab environment to do so?

18.	Review the list of all Docker Swarm services running on the Swarm Cluster.   
`docker service ls`

19.	List the tasks associated with a specific Swarm service.  
`docker service ps nginx`

20.	Scale up the number of container replicas in the nginx service:  
`docker service update nginx --replicas 5`  
Now run the service ps command from step 20 and notice the additional tasks. 
    ```
    ID            NAME     IMAGE                     NODE    DESIRED STATE  
    n16soe8tx52a  nginx.1  satoms/hello-world:swarm  node-2  Running 
    i4vs5mzzxrp1  nginx.2  satoms/hello-world:swarm  node-1  Running 
    iwz5706bzs9r  nginx.3  satoms/hello-world:swarm  node-0  Running 
    47xhonzgs5qq  nginx.4  satoms/hello-world:swarm  node-2  Running 
    9wyexrfnmdje  nginx.5  satoms/hello-world:swarm  node-1  Running 
    ```

21.	From a browser or using curl, validate the web applications are up and running. Note how the hostname reported changes as requests are serviced by one or another replica. 

22.	Remove the services from the lab environment:  
`docker service rm nginx viz`  
This operation will terminate all replicas and delete the service.  Keep the visualizer running for insights into the state of the Swarm cluster in further exercises.

### Lab Complete!

<!-- 
LastTested: 2018-09-28
OS: Ubuntu 18.04
DockerVersion: 18.06.1-ce, build e68fc7a
-->
