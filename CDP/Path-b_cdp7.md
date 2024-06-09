Path B (CDP)
------------

* Launch the CentOS 7 (x86_64) - with Updates HVM with 70 gb storage
* Prerequisite

```
sudo yum update -y
sudo yum install wget -y
sudo yum install java-1.8.0-openjdk-devel.x86_64 -y 

```

### Kernal Tunning 

* VM Swappiness

```
sudo sysctl -a | grep vm.swappiness

 sudo su -c 'cat >>/etc/sysctl.conf <<EOL
 'vm.swappiness=1' 
EOL'

sudo sysctl -p

sudo sed -i '/exit 0/d' /etc/rc.local
```

* THP 

```
sudo su -c 'cat >>/etc/rc.local <<EOL
if test -f /sys/kernel/mm/transparent_hugepage/enabled; then
  echo never > /sys/kernel/mm/transparent_hugepage/enabled
fi
if test -f /sys/kernel/mm/transparent_hugepage/defrag; then
   echo never > /sys/kernel/mm/transparent_hugepage/defrag 
fi
exit 0
EOL'
sudo -i
```




### Database Setup for CDP

* MySQL DB 

```
wget https://downloads.mysql.com/archives/get/p/3/file/mysql-connector-java-5.1.49.tar.gz
tar zxvf mysql-connector-java-5.1.49.tar.gz
sudo mkdir -p /usr/share/java/
cd mysql-connector-java-5.1.49
sudo cp mysql-connector-java-5.1.49/mysql-connector-java-5.1.49-bin.jar /usr/share/java/mysql-connector-java.jar
```

* Setup The Mysql Server (On Database Host)

```
wget https://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm

md5sum mysql57-community-release-el7-9.noarch.rpm

sudo rpm -ivh mysql57-community-release-el7-9.noarch.rpm

sudo yum install --nogpgcheck mysql-server -y

sudo systemctl start mysqld

sudo systemctl status mysqld

sudo mysql_secure_installation

mysqladmin -u root -p version

mysql -u root -p

exit;

```

* DB and user creation for CDP7 

```
CREATE DATABASE scm DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
GRANT ALL ON scm.* TO 'scm'@'%' IDENTIFIED BY 'P@ssw0rd';

SHOW DATABASES;

create database hive DEFAULT CHARACTER SET utf8;
grant all on hive.* TO 'hive'@'%' IDENTIFIED BY 'P@ssw0rd';

create database hue DEFAULT CHARACTER SET utf8;
grant all on hue.* TO 'hue'@'%' IDENTIFIED BY 'P@ssw0rd';

create database rman DEFAULT CHARACTER SET utf8;
grant all on rman.* TO 'rman'@'%' IDENTIFIED BY 'P@ssw0rd';

create database navs DEFAULT CHARACTER SET utf8;
grant all on navs.* TO 'navs'@'%' IDENTIFIED BY 'P@ssw0rd';

create database navms DEFAULT CHARACTER SET utf8;
grant all on navms.* TO 'navms'@'%' IDENTIFIED BY 'P@ssw0rd';

create database oozie DEFAULT CHARACTER SET utf8;
grant all on oozie.* TO 'oozie'@'%' IDENTIFIED BY 'P@ssw0rd';

create database actmo DEFAULT CHARACTER SET utf8;
grant all on actmo.* TO 'actmo'@'%' IDENTIFIED BY 'P@ssw0rd';

create database sentry DEFAULT CHARACTER SET utf8;
grant all on sentry.* TO 'sentry'@'%' IDENTIFIED BY 'P@ssw0rd';

CREATE USER 'temp'@'%' IDENTIFIED BY 'P@ssw0rd';

CREATE DATABASE ranger;
CREATE USER 'rangeradmin'@'%' IDENTIFIED BY 'P@ssw0rd';
CREATE USER 'rangeradmin'@'localhost' IDENTIFIED BY 'P@ssw0rd';
CREATE USER 'rangeradmin'@'<Ranger Admin Role hostname>' IDENTIFIED BY 'P@ssw0rd';
GRANT ALL PRIVILEGES ON ranger.* TO 'rangeradmin'@'%';
GRANT ALL PRIVILEGES ON ranger.* TO 'rangeradmin'@'localhost';
FLUSH PRIVILEGES;



GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'temp'@'%' WITH GRANT OPTION;

Show databases;

```


* Postgressql DB 

    * [Installing Postgres JDBC Driver](https://docs.cloudera.com/cdp-private-cloud-base/7.1.9/installation/topics/cm_postgres_jdbc_installation.html)
    * [Configuring and Starting the PostgreSQL Server](https://docs.cloudera.com/cdp-private-cloud-base/7.1.9/installation/topics/cdpdc-configuring-starting-postgresql-server.html)


* Java Connector "JDBC" 

```
CREATE ROLE scm LOGIN PASSWORD 'P@ssw0rd';
CREATE DATABASE scm OWNER scm ENCODING 'UTF8';
GRANT ALL PRIVILEGES ON DATABASE ranger TO rangeradmin;

CREATE ROLE rman LOGIN PASSWORD 'P@ssw0rd';
CREATE DATABASE rman OWNER rman ENCODING 'UTF8';

CREATE ROLE rangeradmin LOGIN PASSWORD 'P@ssw0rd';
CREATE DATABASE ranger OWNER rangeradmin ENCODING 'UTF8';
GRANT ALL PRIVILEGES ON DATABASE ranger TO rangeradmin;

CREATE ROLE hue LOGIN PASSWORD 'P@ssw0rd';
CREATE DATABASE hue OWNER hue ENCODING 'UTF8';

CREATE ROLE hive LOGIN PASSWORD 'P@ssw0rd';
CREATE DATABASE hive OWNER hive ENCODING 'UTF8';

CREATE ROLE oozie LOGIN PASSWORD 'P@ssw0rd';
CREATE DATABASE oozie OWNER oozie ENCODING 'UTF8';

CREATE ROLE schemaregistry LOGIN PASSWORD 'P@ssw0rd';
CREATE DATABASE schemaregistry OWNER schemaregistry ENCODING 'UTF8';

ALTER DATABASE hive SET standard_conforming_strings=off;
ALTER DATABASE oozie SET standard_conforming_strings=off;

GRANT ALL PRIVILEGES ON DATABASE scm TO scm;
GRANT ALL PRIVILEGES ON DATABASE rman TO rman;
GRANT ALL PRIVILEGES ON DATABASE hue TO hue;
GRANT ALL PRIVILEGES ON DATABASE hive TO hive;
GRANT ALL PRIVILEGES ON DATABASE oozie TO oozie;
GRANT ALL PRIVILEGES ON DATABASE schemaregistry TO schemaregistry;
create database registry;
CREATE USER registry WITH PASSWORD 'P@ssw0rd';
GRANT ALL PRIVILEGES ON DATABASE "registry" to registry;

create database streamsmsgmgr;
CREATE USER streamsmsgmgr WITH PASSWORD 'P@ssw0rd';
GRANT ALL PRIVILEGES ON DATABASE "streamsmsgmgr" to streamsmsgmgr;

```

* Setup The Cloudera Manager Server (On CM Host)

* Setup apache webserver 

```
sudo yum install httpd -y 
systemctl start httpd
systemctl enable httpd
systemctl status httpd
```

* Download CDP Parcels 

```
wget <link of your parcels>

tar -zxvf cdp7.11.3.0.tar.gz 

cp -rp cdp7.11.3.0 /var/www/html 

```

* Got to your web browser access your repo `http://<public_ip>/cdp7.11.3.0`

* `ssh cm` (connect the cm host) 

* Create cloudera mananger repo `sudo vi /etc/yum.repos.d/cloudera-repo.repo`

```
[cloudera-repo]
name=cloudera-repo
baseurl= http://ec2-18-189-26-83.us-east-2.compute.amazonaws.com/cdp7.11.3.0/p/cm7.11.3.0-rhel7/
enabled=1
gpgcheck=0

```

* Install Cloudera Manager Server, Agent and Daemons 

```
sudo yum clean all

sudo yum makecache

sudo yum install -y cloudera-manager-server cloudera-manager-daemons

```

* Run DB Prepare Script 

```
# MySQL 
sudo /opt/cloudera/cm/schema/scm_prepare_database.sh mysql -h localhost scm scm P@ssw0rd

# Postgres
sudo /opt/cloudera/cm/schema/scm_prepare_database.sh postgresql scm scm P@ssw0rd

```

* Start Cloudera Manger Server 

```
systemctl start cloudera-scm-server
systemctl status cloudera-scm-server
```

* Open Web browser `http://<public_ip>:7180/`

* Login with Username `admin` and Password `admin`


* [How-to: Deploy Apache Hadoop Clusters Like a Boss](https://blog.cloudera.com/how-to-deploy-apache-hadoop-clusters-like-a-boss/)
* [Runtime Cluster Hosts and Role Assignments](https://docs.cloudera.com/cdp-private-cloud-base/7.1.9/installation/topics/cdpdc-runtime-cluster-hosts-role-assignments.html?)