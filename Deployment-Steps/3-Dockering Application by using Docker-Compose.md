1) What is the use of Docker-compose.yml
        --> We have to create number of Container(Services) in one single file & Run Simultaniously After Run the Application in only one Compose-file.


2) docker-compose.yml  file 

version: '3'
services:
  backend:
    # build:
      # context: .
    image: '9881665778/flask:latest'
    ports:
      - "5000:5000"
    environment:
      MYSQL_HOST: mysql
      MYSQL_USER: admin
      MYSQL_PASSWORD: admin
      MYSQL_DB: myDb
    depends_on:
      - mysql

  mysql:
    image: 'mysql:5.7'
    environment:
      MYSQL_ROOT_PASSWORD: admin
      MYSQL_DATABASE: myDb
      MYSQL_USER: admin
      MYSQL_PASSWORD: admin
    volumes:
      - './message.sql:/docker-entrypoint-initdb.d/message.sql'   # Mount sql script into container's /docker-entrypoint-initdb.d directory to get table automatically created
      - 'mysql-data:/var/lib/mysql'  # Mount the volume for MySQL data storage

volumes:
  mysql-data:


3) How to run application using compose file

docker-compose up -d

4) How to stop application using compose file

docker-compose down