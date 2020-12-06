README.md
----------

'Hello World' Web Application Version 1.0
=============================================

Description :-

This project prints a 'Hello World' statement when a web browser connects to URL http://192.168.99.101:30080 for the Development environment. 
It prints the 'Hello World' statement by retrieving the text from a MySQL database.  

The same concepts apply for the Production environment just that the web application is connecting to a different database server.  
The Production URL is http://192.168.99.101:30081.  

Bundled Files :-

After cloning the Git repository using the command - git clone https://github.com/dkelim1/webapps-dkelim.git, below files will be presented in webapps-dkelim.  

ddl@dockers:~/webapps-dkelim$ ls -ltr
total 2020
-rw-rw-r-- 1 ddl ddl   50284 Dec  6 14:55 Artifact_HelloWorld_Test.png  <-- Evidence for the working solution.
-rw-rw-r-- 1 ddl ddl   10979 Dec  6 15:37 Prod_Steps.txt   <-- Sample steps for Production deployment  
-rw-rw-r-- 1 ddl ddl  998838 Dec  6 14:55 Development.zip  <-- Archive for the Development environment. 
-rw-rw-r-- 1 ddl ddl      41 Dec  6 14:55 README.md        <-- Instructions on how to deploy the solution.    
-rw-rw-r-- 1 ddl ddl 1001196 Dec  6 14:55 Production.zip   <-- Archive for the Development environment. 


Upon unzipping the 2 archives, below contents will be presented.  


ddl@dockers:~/webapps-dkelim/Development$ ls -ltr
total 12
drwxrwxr-x 4 ddl ddl 4096 Dec  6  2020 dev-webapps <-- HELM Charts for Web application
drwxrwxr-x 4 ddl ddl 4096 Dec  6  2020 dev-mysqldb <-- HELM Charts for MySQL DB
drwxrwxr-x 4 ddl ddl 4096 Dec  6  2020 nodejs      <-- The Web application itself including the DockerFile

ddl@dockers:~/webapps-dkelim/Production$ ls -ltr
total 12
drwxrwxr-x 5 ddl ddl 4096 Dec  6  2020 prod-webapps
drwxrwxr-x 4 ddl ddl 4096 Dec  6  2020 prod-mysqldb
drwxrwxr-x 4 ddl ddl 4096 Dec  6  2020 nodejs


Pre-requisites :-

- A stable Internet connection to download the necessary softwares/docker images.  
- A working Kubernete Cluster running on minikube.  
- HELM installed for deployment and templating.  


Procedures :-

Note that 

a) for Production deployment, the same steps can be applied by changing the respective HELM chart folders respectively. 
For eg, dev-webapps becomes prod-webapps.  

b) the below steps need to be executed at the machine that hosts the minikube service as a normal user.  In my case this is the MacBook itself which 
hosts the minikube service running within a VirtualBox VM.      

 
1) After cloning the repository, navigate to the webapps-dkelim folder.  
merrylee:~ merrylee$ cd webapps-dkelim; ls -l 

You will see the below files.

 merrylee:~/webapps-dkelim merrylee$ ls -ltr
total 2012
-rw-rw-r-- 1 ddl ddl   50284 Dec  6 14:55 Artifact_HelloWorld_Test.png
-rw-rw-r-- 1 ddl ddl   10979 Dec  6 15:37 Prod_Steps.txt
-rw-rw-r-- 1 ddl ddl  998838 Dec  6 14:55 Development.zip
-rw-rw-r-- 1 ddl ddl      41 Dec  6 14:55 README.md
-rw-rw-r-- 1 ddl ddl 1001196 Dec  6 14:55 Production.zip
  

2) Unzip the Development.zip archive.  Then navigate to the Development folder.  

merrylee:~/webapps-dkelim merrylee$ unzip Development.zip
Archive:  Development.zip
   creating: Development/
   creating: Development/dev-mysqldb/
  inflating: Development/dev-mysqldb/.helmignore
  inflating: Development/dev-mysqldb/Chart.yaml
   creating: Development/dev-mysqldb/charts/
   creating: Development/dev-mysqldb/templates/
  inflating: Development/dev-mysqldb/templates/config.yaml
  inflating: Development/dev-mysqldb/templates/Deployment.yaml
  inflating: Development/dev-mysqldb/templates/pvc.yaml
  inflating: Development/dev-mysqldb/templates/service.yaml
  inflating: Development/dev-mysqldb/values.yaml
   creating: Development/dev-webapps/
  inflating: Development/dev-webapps/.helmignore
  inflating: Development/dev-webapps/Chart.yaml
   creating: Development/dev-webapps/charts/
   creating: Development/dev-webapps/templates/
  inflating: Development/dev-webapps/templates/deployment.yaml
  inflating: Development/dev-webapps/templates/service.yaml
  inflating: Development/dev-webapps/templates/service_ext.yaml
  inflating: Development/dev-webapps/values.yaml
  ...
  ....
  ......

merrylee:~/webapps-dkelim merrylee$ cd Development; ls -ltr
total 12
drwxrwxr-x 4 ddl ddl 4096 Dec  6  2020 dev-webapps 
drwxrwxr-x 4 ddl ddl 4096 Dec  6  2020 dev-mysqldb 
drwxrwxr-x 4 ddl ddl 4096 Dec  6  2020 nodejs      

3) Execute the mysql deployment by executing the below command.   
 
merrylee:~/webapps-dkelim/Development merrylee$ helm install dev-mysqldb ./dev-mysqldb
NAME: dev-mysqldb
LAST DEPLOYED: Sun Dec  6 14:32:50 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None

4) Check that the mysql deployment is running properly as follow.  

merrylee:~/webapps-dkelim/Development merrylee$ kubectl get all
NAME                              READY   STATUS    RESTARTS   AGE
pod/dev-mysqldb-ddff4c64f-qxwvz   1/1     Running   0          21m

NAME                  TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
service/dev-mysqldb   ClusterIP   10.104.191.68   <none>        3306/TCP   21m
service/kubernetes    ClusterIP   10.96.0.1       <none>        443/TCP    4d16h

NAME                          READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/dev-mysqldb   1/1     1            1           21m

NAME                                    DESIRED   CURRENT   READY   AGE
replicaset.apps/dev-mysqldb-ddff4c64f   1         1         1       21m


5) Configure mysql DB.
Retrieve the random root password.  
   
$ logs dev-mysqldb-ddff4c64f-qxwvz

Note down the random root password from the below line.  Password is after 'PASSWORD: '.    
[Entrypoint] GENERATED ROOT PASSWORD: UqjyBuR5Yw]YJ@PiqIf@x@j-iJ^  

Launch a root shell to the mysql DB pod.  
$ kubectl exec --stdin --tty dev-mysqldb-ddff4c64f-qxwvz -- /bin/bash

Login to the mysql console.  
# mysql -h localhost -uroot -p
   

Eg :-

merrylee:~/webapps-dkelim/Development merrylee$ kubectl logs dev-mysqldb-ddff4c64f-qxwvz
[Entrypoint] MySQL Docker Image 8.0.22-1.1.18
[Entrypoint] No password option specified for new database.
[Entrypoint]   A random onetime password will be generated.
[Entrypoint] Initializing database
2020-12-06T06:33:00.006528Z 0 [System] [MY-013169] [Server] /usr/sbin/mysqld (mysqld 8.0.22) initializing of server in progress as process 20
2020-12-06T06:33:00.014637Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2020-12-06T06:33:00.748498Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2020-12-06T06:33:02.386019Z 6 [Warning] [MY-010453] [Server] root@localhost is created with an empty password ! Please consider switching off the --initialize-insecure option.
[Entrypoint] Database initialized
2020-12-06T06:33:06.537633Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.0.22) starting as process 63
2020-12-06T06:33:06.557228Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2020-12-06T06:33:06.774297Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2020-12-06T06:33:06.946292Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Socket: /var/run/mysqld/mysqlx.sock
2020-12-06T06:33:07.147791Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
2020-12-06T06:33:07.148183Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
2020-12-06T06:33:07.200939Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.0.22'  socket: '/var/lib/mysql/mysql.sock'  port: 0  MySQL Community Server - GPL.
Warning: Unable to load '/usr/share/zoneinfo/iso3166.tab' as time zone. Skipping it.
Warning: Unable to load '/usr/share/zoneinfo/leapseconds' as time zone. Skipping it.
Warning: Unable to load '/usr/share/zoneinfo/tzdata.zi' as time zone. Skipping it.
Warning: Unable to load '/usr/share/zoneinfo/zone.tab' as time zone. Skipping it.
Warning: Unable to load '/usr/share/zoneinfo/zone1970.tab' as time zone. Skipping it.
[Entrypoint] GENERATED ROOT PASSWORD: UqjyBuR5Yw]YJ@PiqIf@x@j-iJ^   

merrylee:~/webapps-dkelim/Development merrylee$ kubectl exec --stdin --tty dev-mysqldb-ddff4c64f-qxwvz   -- /bin/bash

bash-4.2# mysql -h localhost -uroot -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 9
Server version: 8.0.22

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 


6) Change root user password in mysql by executing the below SQL queries.  

ALTER USER 'root'@'localhost' IDENTIFIED BY 'password';


Eg :-

mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'password';
Query OK, 0 rows affected (0.01 sec)


7) Create required database and table by executing the below SQL queries.  

<<< Start of SQL queries >>>

CREATE DATABASE messages;
use messages;
CREATE TABLE messages(text VARCHAR(100));
SHOW TABLES;
INSERT INTO messages VALUES ('Hello World');
select * from messages;

<<< End of SQL queries >>>

Eg :-

mysql> CREATE DATABASE messages;
Query OK, 1 row affected (0.00 sec)

mysql> use messages;
Database changed
mysql> CREATE TABLE messages(text VARCHAR(100));
Query OK, 0 rows affected (0.02 sec)

mysql> SHOW TABLES;
+--------------------+
| Tables_in_messages |
+--------------------+
| messages           |
+--------------------+
1 row in set (0.00 sec)

mysql> INSERT INTO messages VALUES ('Hello World');
Query OK, 1 row affected (0.01 sec)

mysql> select * from messages;
+-------------+
| text        |
+-------------+
| Hello World |
+-------------+
1 row in set (0.00 sec)

8) Grant permissions for root and mysqluser by executing the below SQL queries.  'mysqluser' is the account that will 
login to the database from the web application.  

<<< Start of SQL queries >>>

CREATE USER 'root'@'%'
  IDENTIFIED BY 'password';
GRANT ALL
  ON *.*
  TO 'root'@'%'
  WITH GRANT OPTION;

ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'password';


CREATE USER 'mysqluser'@'%'
  IDENTIFIED BY 'password';
GRANT ALL
  ON messages.*
  TO 'mysqluser'@'%'
  WITH GRANT OPTION;


ALTER USER 'mysqluser'@'%' IDENTIFIED WITH mysql_native_password BY 'password';

FLUSH PRIVILEGES;

exit

<<< End of SQL queries >>>

At this point, the database setup is completed.  Exit to the minikube host by typing 'exit'.  

Eg :-

mysql> CREATE USER 'root'@'%'
    ->   IDENTIFIED BY 'password';
Query OK, 0 rows affected (0.01 sec)

mysql> GRANT ALL
    ->   ON *.*
    ->   TO 'root'@'%'
    ->   WITH GRANT OPTION;
Query OK, 0 rows affected (0.01 sec)

mysql>
mysql> ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
Query OK, 0 rows affected (0.01 sec)

mysql>
mysql>
mysql> CREATE USER 'mysqluser'@'%'
    ->   IDENTIFIED BY 'password';
Query OK, 0 rows affected (0.01 sec)

mysql> GRANT ALL
    ->   ON messages.*
    ->   TO 'mysqluser'@'%'
    ->   WITH GRANT OPTION;
Query OK, 0 rows affected (0.00 sec)

mysql>
mysql>
mysql> ALTER USER 'mysqluser'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
Query OK, 0 rows affected (0.01 sec)

mysql>
mysql> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.01 sec)

mysql> exit
Bye
bash-4.2# exit
exit
merrylee:~ merrylee$


9) Execute the web application deployment by executing the below command.   
 
merrylee:~/webapps-dkelim/Development merrylee$  helm install  dev-webapps ./dev-webapps
NAME: dev-webapps
LAST DEPLOYED: Sun Dec  6 15:07:23 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None

10) Check that the web deployment is running properly as follow.  

merrylee:~/webapps-dkelim/Development merrylee$ kubectl get all
NAME                               READY   STATUS    RESTARTS   AGE
pod/dev-mysqldb-ddff4c64f-qxwvz    1/1     Running   0          35m
pod/dev-webapps-76f7567cb4-b48sr   1/1     Running   0          61s
pod/dev-webapps-76f7567cb4-qxfm7   1/1     Running   0          61s
pod/dev-webapps-76f7567cb4-ztbzm   1/1     Running   0          61s

NAME                      TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
service/dev-mysqldb       ClusterIP   10.104.191.68   <none>        3306/TCP       35m
service/dev-webapps       ClusterIP   10.103.80.254   <none>        80/TCP         61s
service/dev-webapps-ext   NodePort    10.106.67.208   <none>        80:30080/TCP   61s
service/kubernetes        ClusterIP   10.96.0.1       <none>        443/TCP        4d16h

NAME                          READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/dev-mysqldb   1/1     1            1           35m
deployment.apps/dev-webapps   3/3     3            3           61s

NAME                                     DESIRED   CURRENT   READY   AGE
replicaset.apps/dev-mysqldb-ddff4c64f    1         1         1       35m
replicaset.apps/dev-webapps-76f7567cb4   3         3         3       61s

11) Retrieve the public URL to connect from outside the minikube environment by executing the below command.    

merrylee:~/webapps-dkelim/Development merrylee$ minikube service dev-webapps-ext --url
http://192.168.99.101:30080

12) For an initial test, utilize 'curl' to access the URL.  The 'Hello World' text should be displayed.  

merrylee:~/webapps-dkelim/Development merrylee$ curl http://192.168.99.101:30080
[{"text":"Hello World"}]merrylee:~/webapps-dkelim/Development merrylee$

See file 'Artifact_HelloWorld_Test.png' for screenshots taken from other hosts.  

=====================================================================

Future Enhancements :-

1) The database hostname, connection user and password are hard coded in the app.js script.  For Development and Production environment, it will have different values.  
   A more elegant way is to pass in the parameters as  environment variables so that the script need not be updated when there is an environment change.  
   
   // parse application/json
   app.use(bodyParser.json());
 
   //create database connection
   const conn = mysql.createConnection({
   host: 'db', <---
   user: 'mysqluser', <---
   password: 'password', <---
   database: 'messages'
   });
   
2) Build the dependency in the HELM' s project so that the web application depends on the database service to startup.  

3) Build the CI component utilizing Jenkin pipelines.  

