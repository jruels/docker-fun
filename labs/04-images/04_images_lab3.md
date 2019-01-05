# LAB
## Docker Images Part 3

*Lab Objectives*

This lab demonstrates how to add, change and delete files within a container. Students will inspect the changes as they are made to a docker image and it’s impacts on the Union FileSystem.

Lab Structure - Overview
1.	Inspect Changes in to a docker image and the Union FileSystem
 
### 1. Inspect Changes in a Filesystem
Step by Step Guide
1.	Locate the IP address of the Master machine within the lab folder.

2.	If on a Mac, or using Linux:
In a command line, enter  
`ssh -i </Users/…/>docker.pem ubuntu@<IP>`  
The .pem file will be provided by the instructor for this lab. This command will connect the console to the Docker machine.

*If using Windows: Open Putty and connect to the session you saved earlier.*
 

3.	First, check the system to verify the image Busybox has been pulled to the machine.  
    `docker images busybox`  
If it is not, pull it from Docker Hub:  
    `docker pull busybox`

4.	View the history of the image:  
    `docker history busybox`

5.	Start and enter a new container called 0mb (zero megabytes):  
    `docker run -it --name 0mb busybox /bin/ash`  
Create a new file inside the container that is 100MB in size and exit. The following command will do this for you.   
    `dd if=/dev/zero of=100mb_file bs=102400000 count=1`  
    `exit`  

6.	Inspect the changes made to the container:  
    `docker diff 0mb`  
Output:  
    ```
    A /100mb_file
    C /root
    A /root/.ash_history
    ```

7.	Create a new image from the changes made to the container and tag it with 100:  
    `docker commit -m "created 100mb file" -a <your name> 0mb startbox:100`

8.	View the new image and note the size of the image:  
    `docker images startbox`

9.	Start and enter the startbox:100 container:  
    `docker run -it --name 100mb startbox:100 /bin/ash`  
Change the file inside the container and exit:  
    `dd if=/dev/zero of=100mb_file bs=102400000 count=1`  
    `exit`

10.	Inspect the changes made to the container:  
    `docker diff 100mb`  
Output:
    ```
    C /100mb_file
    C /root
    C /root/.ash_history
    ```

11.	Create a new image from the changes made to the container and tag it with 200:  
    `docker commit -m "created the same 100mb file" -a <your name> 100mb startbox:200`

12.	View the images created from the startbox repository:  
    `docker images startbox`

13.	Start and enter the startbox:200 container and remove the 100mb file made in the previous steps. This should result in a reduction in image size, but doesn’t. Why?  
    `docker run -it --name 200mb startbox:200 /bin/ash`  
Remove the file and exit:  
    `rm 100mb_file`  
    `exit`  

14.	Inspect the changes made to the container. Note the deleted 100MB file.  
    `docker diff 200mb`  
Output:
    ```
    D /100mb_file
    C /root
    C /root/.ash_history
    ```

15.	Create a new image from the changes made to the container and tag it with 300:  
    `docker commit -m "deleted the 100mb file" -a <your name> 200mb startbox:300`

16.	View the images created with the startbox name. Note the image sizes, even though a 100mb file was deleted, the image did not get smaller. This is because of the copy-on-write features of the Union FileSystem.  
    `docker images startbox`

17.	View the history of the latest image:  
    `docker history startbox:300`

18.	Delete the containers that you created in this exercise using 'docker rm'. Are any other flags needed?  
    `docker rm 0mb 100mb 200mb`

18.	Delete the startbox images from the local image cache.  
    `docker rmi startbox:300 startbox:200 startbox:100`

### Lab Complete!

<!-- 
LastTested: 2018-09-28
OS: Ubuntu 18.04
DockerVersion: 18.06.1-ce, build e68fc7a
-->