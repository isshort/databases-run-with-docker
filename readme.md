# Mysql
first go to mysql directory 
        
        cd mysql

and then.
    
    $ docker-compose build 
    $ docker-compose up
    
### Note 
if port is already used in your computer change port number in docker-compose.yml file 

check mysql with adminer 

    http://localhost:8080

Connection with Adminer and MySQL
    
    “System” : select “MySQL” ;
    “Server” : type “mysql” ;
    “Username” : type “root” ;
    “Password” : type “12345678” ;
    “Database” : type “mydb” ;
### Note
    these credentials we have set in docker-compose.yml file you can change it 
Run mysql with phpmyadmin 

    http://localhost:8081

    server : mysql
    username: root
    password: 12345678

### Notethese credentials we have set in docker-compose.yml file you can change it 
# Postgres
    go to postgresql directory 

    and then.
    
    $ docker-compose build 
    $ docker-compose up

### Note if port is already used in your computer change port number in docker-compose.yml file 

    check postgresql with pgadmin 

    http://localhost:5050

    login:  admin@admin.com
    password: 12345678

### Note
    these credentials we have set in docker-compose.yml file you can change it 


