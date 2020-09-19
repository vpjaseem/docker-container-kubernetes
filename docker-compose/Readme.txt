This will create a container using 'docker-compose.yml' file. Thios file is used instead of 'docker container run' command.

Step 1: Get the file
- Download the 'docker-compose.yml' from this directory. Alternatively, you can copy paste below and create your own .yml file. 
//
version: '3.1'
services:
  web-app:
    image: vpjaseem/httpd-custom-website
    domainname: web-app.com
    hostname: web-app
    ports:
      - '80:80'
//

- This docker-compose.yml uses 'vpjaseem/httpd-custom-website' image to create a container.

Step 2: Build Container
- Navigate to the location where you have the 'docker-compose.yml'

//
[root@localhost Desktop]# docker-compose up -d
//

- If your file name is something else (e.g 'docker-compose-httpd-web-app.yml'), then use '-f' with 'docker-compose' command.

//
[root@localhost Desktop]# docker-compose -f docker-compose-httpd.yml up -d
//

Step 3: Clean-up
- To clean up all containers created by 'docker-container up' command.
- This won't delete other containters.

//
[root@localhost Desktop]# docker-compose down
//

OR

//
[root@localhost Desktop]# docker-compose -f docker-compose-httpd.yml down
//


Optional:
- If you want to map a local direcotry to the html root of container, use below code as 'docker-compose.yml'.
- In my example, I have created a folder called 'web-app-wwwroot' on my Docker Engine desktop and mapped to container html directory.

//
version: '3.1'
services:
  web-app:
    image: vpjaseem/httpd-custom-website
    container_name: web-app
    domainname: web-app.com
    hostname: web-app
    volumes:
      - /root/Desktop/web-app-wwwroot:/usr/local/apache2/htdocs
    ports:
      - '80:80'
//
