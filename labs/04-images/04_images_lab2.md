## Docker Images Part 2

*Lab Objectives*

This lab demonstrates how to build and run WordPress All-In-One using a Dockerfile.

Lab Structure - Overview
1.	Use a Dockerfile to build and run the WordPress All-In-One image.


### 1. Build and Run WordPress All-In-One
1.	Using VS Code, create and navigate to a folder that will be used to store the files for this lab. For example:  
   `wp-aio-cli`  
2.	Using the VS Code editor, create a file named `Dockerfile` with the following:  



```dockerfile
FROM aslaen/wpaio
LABEL activity="wp-cli-install"

RUN apt-get update &&\
    apt-get install wget
RUN wget https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar
RUN mv wp-cli.phar /usr/local/bin/wp && chmod +x /usr/local/bin/wp
```

3.	Save the Dockerfile.   

3. Build a new image using the Dockerfile:  
   `docker build -t wordpress-cli:aio-dockerfile .`  
   *NOTE: you must add the period at the end*  
   The output will list each step from the Dockerfile as it completes and give an image ID when the build is successful.

   ```
   Sending build context to Docker daemon 2.048 kB
   Step 1 : FROM lindison/wordpress-aio
   ---> 2740960e64da
   Step 2 : LABEL activity "wp-cli-install"
   ---> Using cache
   ---> 2d73ad8d6742
   Step 3 : RUN apt-get update &&    apt-get install wget
   ---> Using cache
   ---> 56d349b38274
   Step 4 : RUN wget wget https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar
   ---> Using cache
   ---> dbb6c6231ea9
   Step 5 : RUN mv wp-cli.phar /usr/local/bin/wp && chmod +x /usr/local/bin/wp
   ---> Using cache
   ---> 51a742a8a163
   Successfully built 51a742a8a163
   Successfully tagged wordpress-cli:aio-dockerfile
   ```

4. The following command will list the images currently on the machine. The new image `wordpress-cli:aio-dockerfile` is listed among them.  

   `docker images`

5. Deploy a container using the image that was created from the image build’s context defined in the Dockerfile (`wordpress-cli:aio-dockerfile`).  
   `docker run -d -P wordpress-cli:aio-dockerfile`

6. Enter  
   `docker port $(docker ps -lq)`  
   Two ports will be listed:  

   ```
   3306/tcp -> 0.0.0.0:32779  
   80/tcp -> 0.0.0.0:32780  
   ```

7. In a web browser, navigate to http://`<IP_Address>`:port using the local host IP address of your machine and the port mapped to 80/tcp from the previous step (i.e. 32780).

8. Configure WordPress with the following credentials:

   `Username: root`  
   `Password: root`

9. Test the install again with the following execute command:  
   `docker exec $(docker ps -lq) wp theme list --allow-root --path='/var/www/html'`  
   The output will look like this:

   ```
   name	status	update	version	update_version	auto_update
   twentynineteen	inactive	available	1.4	3.1	off
   twentyseventeen	inactive	available	2.2	3.9	off
   twentytwenty	active	available	1.0	2.9	off
   ```

10. Run a history of the wordpress-cli:aio-dockerfile image:  
    `docker history wordpress-cli:aio-dockerfile`

11. Run a history of the wordpress-cli:aio-manual image:  
    `docker history <dockerhubusername>/wordpress-cli:aio-manual`

12. Run a diff on the container made from the `wordpress-cli:aio-dockerfile` image:  
    `docker diff <container ID>`

13. Run a diff on the container made from the `wordpress-cli:aio-manual` image. Note the different A (Adds), C (Changes), and D (Deletes). Which changes did you make in the previous manual build lab? Which changes appear to be made by WordPress running?   
    `docker diff <container ID>`

14. Finally, use the `docker rm` command to remove the containers from this lab and clean up the environment. For more information, enter  
    `docker --help`

### Lab Complete!
