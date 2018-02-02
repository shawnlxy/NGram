# NGram

This project implements Google Search’s Auto Completion function with Hadoop MapReduce

## Prerequisites 

* [MAMAP: My Apache - MySQL - PHP](https://www.mamp.info/en/) - Used to connect web interface and mysql
* [Docker](https://docs.docker.com/) - Container

### How to build hadoop cluster on Docker

```
$ mkdir bigdata
$ cd bigdata
$ sudo docker pull joway/hadoop-cluster 
$ git clone https://github.com/joway/hadoop-cluster-docker 
$ sudo docker network create --driver=bridge hadoop 
$ cd hadoop-cluster-docker

$ sudo ./start-container.sh

$ ./start-hadoop.sh
```
### Mysql Guide
```
look up MYSQL port:
check in MAMP or enter the command line in MYSQL:

$ SHOW VARIABLES WHERE Variable_name = 'port' ; 

Configure MYSQL:

$ Open Terminal 
$ cd /Applications/MAMP/Library/bin/
$ ./mysql -uroot -p // enter your password

$ create database test;
$ use test;
$ create table output(starting_phrase VARCHAR(250), following_word VARCHAR(250), count INT); 
$ GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '**_your-password_** ' WITH GRANT OPTION;   //enable remote data transfer
$ FLUSH PRIVILEGES;
```
## Get Started 
Enter hadoop:
```
$ cd hadoop-cluster-docker
$ ./start-container.sh
$ ./start-hadoop.sh
$ cd src

```
download mysql-connector
```
wget http://central.maven.org/maven2/mysql/mysql-connector-java/5.1.39/mysql-connector-java-5.1.39.jar
```

HDFS operations:
```
$ hdfs dfs -mkdir /mysql
$ hdfs dfs -put mysql-connector-java-*.jar /mysql/
$ put NGram folder into hadoop
$ cd NGram
$ hdfs dfs -mkdir -p input
$ hdfs dfs -rm -r /output 
$ hdfs dfs -put bookList/*  input/ 
$ cd src
```
Open Driver.java and modify 4 parameters:

```
local_ip_address  : 192.168.31.16
MySQL_port : 8889
your_password: root
hdfs_path_to_mysql-connector: /mysql/mysql-connector-java-5.1.39-bin.jar

 DBConfiguration.configureDB(conf2,
 "com.mysql.jdbc.Driver", // driver class
 "jdbc:mysql://local_ip_address:MySQL_port/test", // db url
 "root", // user name
 “your_password”); //password

job2.addArchiveToClassPath(new Path(“hdfs_path_to_mysql-connector”));
```
Here is an example:
```
DBConfiguration.configureDB(conf2,
"com.mysql.jdbc.Driver",
"jdbc:mysql://192.168.31.16:8889/test",
"root",
"root");
job2.addArchiveToClassPath(new Path("/mysql/mysql-connector-java-5.1.39-bin.jar"));
```
Run Auto-Complete
```
$ hadoop com.sun.tools.javac.Main *.java
$ jar cf ngram.jar *.class
$ hadoop jar ngram.jar Driver input /output 2 3 4
```

Set at least 3G memory, 3CPU in Docker
```
2 represents ngram_size 
3 represents threashold_size
4 represents following_word_size
```
You can check whether there is output in mysql database:

$ select * from output limit 10 ;



## Running Web Interface

Put autocomplete folder into :
```
ubuntu /usr/local/ammps/www 
mac /Applications/MAMP/htdocs
```
Correct the parameters such as port number in Autocomplete/ajax_refresh.php

Stop Servers then Start Servers in MAMP

You can vist localhost:8888(ubuntu localhost:80, Windows localhost) and choose autocomplete

Demo pic:

![screen shot 2018-01-27 at 6 15 07 pm](https://user-images.githubusercontent.com/36029186/35716128-b0c98dbc-07a4-11e8-82a9-7569c2461736.png)


