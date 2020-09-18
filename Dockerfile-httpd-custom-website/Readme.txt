Use this Dockerfile to build a Docker image. Once you run a container using this image, you will see the custom website.

Step 1: Get the Dockerfile
- Download the Dockerfile in this folder and place in in your Docker Client machine.
- Navigate to the Directory where you placed the Docker file

//
[root@localhost ~]# cd Desktop/
[root@localhost Desktop]# ls -ltr
total 1
-rw-r--r--. 1 root root 465 Sep 18 19:25 Dockerfile
//

Step 2: Build an image based on this Dockerfile
- I have used tagname as vpjaseem/httpd-custom-website since my Docker Hub username is vpjaseem.
//
[root@localhost Desktop]#docker image build -t vpjaseem/httpd-custom-website .
//

Step 4: Start a Container using the Image 'vpjaseem/httpd-custom-website'
//
[root@localhost Desktop]#docker container run --name web-app --detach --publish 80:80 vpjaseem/httpd-custom-website
//

Step 5: Verify the Website
- Open any web browser and browse http://IP_ADDRESS_OF_DOCKER_ENGINE_HOST:80

Step 6: Check the Container logs
//
[root@localhost Desktop]# docker container logs -f web-app 
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.2. Set the 'ServerName' directive globally to suppress this message
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.2. Set the 'ServerName' directive globally to suppress this message
[Fri Sep 18 19:47:58.372679 2020] [mpm_event:notice] [pid 1:tid 140438759867520] AH00489: Apache/2.4.46 (Unix) configured -- resuming normal operations
[Fri Sep 18 19:47:58.373177 2020] [core:notice] [pid 1:tid 140438759867520] AH00094: Command line: 'httpd -D FOREGROUND'
192.168.39.1 - - [18/Sep/2020:19:50:27 +0000] "GET / HTTP/1.1" 304 -
192.168.39.1 - - [18/Sep/2020:19:50:27 +0000] "GET /css/site.css HTTP/1.1" 304 -
192.168.39.1 - - [18/Sep/2020:19:50:27 +0000] "GET /img/successCloudNew.svg HTTP/1.1" 304 -
192.168.39.1 - - [18/Sep/2020:19:50:27 +0000] "GET /img/tweetThis.svg HTTP/1.1" 304 -
192.168.39.1 - - [18/Sep/2020:19:50:27 +0000] "GET /img/cloneWhite.svg HTTP/1.1" 304 -
192.168.39.1 - - [18/Sep/2020:19:50:27 +0000] "GET /img/deployWhite.svg HTTP/1.1" 304 -
192.168.39.1 - - [18/Sep/2020:19:50:27 +0000] "GET /img/stackWhite.svg HTTP/1.1" 304 -
192.168.39.1 - - [18/Sep/2020:19:50:27 +0000] "GET /img/lightbulbWhite.svg HTTP/1.1" 304 -
//
- Hit Cntr + C to exit the logs.

Step 7: Upload the image to Docker Hub
- If you have already logged in to Docker Hub via Docker CLI, you may ignore the login commands.
//
[root@localhost Desktop]# docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: USERNAME_HERE
Password: PASSWORD_HERE
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
[root@localhost Desktop]#

[root@localhost Desktop]#docker push vpjaseem/httpd-custom-website:latest
//

Step 8: Remove the container
//
[root@localhost Desktop]#docker container rm -f web-app
//

Step 9 (Optional): Pull the Image from Docker Hub
- In future if you want to run the same container, pull the image from Docker Hub.

//
[root@localhost Desktop]#docker pull vpjaseem/httpd-custom-website:latest
[root@localhost Desktop]#docker container run --name web-app --detach --publish 80:80 vpjaseem/httpd-custom-website
//
