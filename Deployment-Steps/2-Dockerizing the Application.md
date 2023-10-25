1) What is Docker:-

   - Docker is an Open source tool
   - We can Build, Deploy, Run & Manage our Application
   - Docker created a virtaul environment ----- It means we have created container by using Docker
   - We can easy run our application on virtually (Browser)

   ------------------ Steps--------------------------

   | step 1: Dockerfile                                 |
   | -------------------------------------------------- |
   | OS -Ubuntu  ---use command                         |
   | copy the code ---use command                       |
   | Libraries                                          |
   | command                                            |
   | Run the Application                                |
   | __________________________________________________ |

   Step 2: Dockerfile Created Image (Build)
   step 3: Image Creted Number of Docker container
   -----------------------------------------------

2)---> Step 1): Process of Docker File

    a) Check the present Status by using command
     docker ps

    Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get "http://%2Fvar%2Frun%2Fdocker.sock/v1.24/containers/json": dial unix /var/run/docker.sock: connect: permission denied

    Facing Error: How to solve  by using command 1)whoami   -----> Check the Owner---> ritesh
                                                  2)sudo chown ritesh /var/run/docker.sock    or  sudo chown $USER /var/run/docker.sock ------> Change the Ownership of docker path
                                                  3)docker ps ----> Solve the Error

    CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

    b) How to pull code from GitHub
    git clone https://github.com/LondheShubham153/two-tier-flask-app.git

    c) Create Dockerfile in your Repo.

    +----------------------------------------Dockerfile---------------------------------------------------------+
    |        # Use an official Python runtime as the base image                                        |
    |        FROM python:3.9-slim                                                                                      |
    |                                                                                                                                 |
    |        # Set the working directory in the container                                                    |
    |        WORKDIR /app                                                                                                  |
    |                                                                                                                                 |
    |        # install required packages for system                                                            |
    |        RUN apt-get update \                                                                                        |
    |            && apt-get upgrade -y \                                                                       	|
    |            && apt-get install -y gcc default-libmysqlclient-dev pkg-config \                              |
    |            && rm -rf /var/lib/apt/lists/*                                                                 |
    |                                                                                                           |
    |        # Copy the requirements file into the container                                                    |
    |        COPY requirements.txt .                                                                            |
    |                                                                                                           |
    |        # Install app dependencies                                                                         |
    |        RUN pip install mysqlclient                                                                        |
    |        RUN pip install --no-cache-dir -r requirements.txt                                                 |
    |                                                                                                           |
    |        # Copy the rest of the application code                                                            |
    |        COPY . .                                                                                           |
    |                                                                                                           |
    |        # Specify the command to run your application                                                      |
    |        CMD ["python", "app.py"]                                                                           |
    +-----------------------------------------------------------------------------------------------------------+

   ----> Step 2): Create Image(Build) using Dockerfile by using command

    docker build . -t flaskapp
            docker images

   ----> step 3): Create Container with the help of Image
            docker run -d -p 5000:5000 flaskapp:latest
    |
                    -----> Demain(Detached Mode) (Background) with port (System port:Docker Container port) Run by your Latest Image(flaskapp)

3) How to Connect Your Application to MYSQL (Database)

---->a) First create Mysql Docker Container(MYSQL Database want to Docker Container)

    docker run -d -p 3306:3306 --name mysql -e MYSQL_ROOT_PASSWORD="admin" mysql:5.7
    |
            ----> pull the mysql image 5.7 from DockerHub & run with Detached Mode by default port(3306) by environment gives Root User Password

    docker ps

---->b) Create a One Network & kill the both container then after connect to the network

    1) Create a Network (Comman Network)
           docker network create twotier

    2) Kill the Running both Container
           docker kill`<mysql Container ID>`

    docker kill`<flaskapp container ID>`

----->c) Network Connected to both Container(Mysql & Flask Communicate both container to each other) and run the both container

    1)  Flaskapp Container run with network
            docker run -d -p 5000:5000 --network=twotier -e MYSQL_HOST=mysql -e MYSQL_USER=admin -e MYSQL_PASSWORD=admin -e MYSQL_DB=myDb --name=flaskapp flaskapp:latest

    2)  Mysql (Database) Container run with network
            docker run -d -p 3306:3306 --network=twotier -e MYSQL_DATABASE=myDb -e MYSQL_USER=admin -e MYSQL_PASSWORD=admin -e MYSQL_ROOT_PASSWORD=admin --name=mysql mysql:5.7

    IF Any Continer run in already to remove
            docker rm`<container code>`

    3) Check the present status and network
            docker ps

    docker network ls

    4) How to inspect twotier network and see Continer
            docker network inspect twotier

4) Create Table in your MySQL database and Interact

-----> a) How to Entered in Mysql Container
          docker exec -it `<Container ID>` bash      ----> Execute with Intersective Terminal(-it) open in bash-shell Terminal

    You Entered in Continer
          bash-4.2#

    b) Login with Mysql Database by user Password
           bash-4.2# mysql -u root -p                                  ----------> Password: admin

    You Entered in mysql database
           mysql>

    mysql>show databases;
           +--------------------+
           | Database           |
           +--------------------+
           | information_schema |
           | myDb               |
           | mysql              |
           | performance_schema |
           | sys                |
           +--------------------+

    c) How to use myDb
           mysql> use myDb;
           Database Changed

    d) Create Table and add query
           mysql> CREATE TABLE messages (
                  id INT AUTO_INCREMENT PRIMARY KEY,
                  message TEXT
                  );

    e) How to change permission
           GRANT ALL PRIVILEGES ON*.* TO 'mysql.session'@'localhost' IDENTIFIED BY 'password';

    f) How to show the user & host
        mysql> SELECT User,Host FROM mysql.user;
               +---------------+-----------+
               | User          | Host      |
               +---------------+-----------+
               | mysql.session | %         |
               | mysql.sys     | %         |
               | root          | %         |
               | mysql.sys     | localhost |
               | root          | localhost |
               +---------------+-----------+

    g) How to see messages from database (Create table of message)
         select * from messages;

5) Run the Application on browser:
   http://127.0.0.1:5000/
6) How to push docker image to Docker-Hub

   1) First Login With DockerHub

      docker login
      --> Username: riteshgade16@gmail.com
      --> Password:

   --->Login Succeeded

   2) Create repository on DockerHub, Tag The Image & Push the image

      docker tag flaskapp:latest 9881665778/flask
      docker images
      docker push 9881665778/flask:latest    -----> latest is `<tag name>`
