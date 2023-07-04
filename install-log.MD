su -



apt install git
apt install maven


-- jdk11 pre-installed

git clone https://github.com/indexdata/localindices

cd localindices
mvn install


Install jdk8:  https://adoptium.net/temurin/releases/


Install ssh

ssh 127.0.0.1 -p 2221

Install Mariadb 10.5 or MySQL 8

***

Vagrant box:

The vagrant box is debian 9 (stretch), cannot update to newer Mariadb on that. 




***


New Vagrant box - Ubuntu Focal (20)

 vagrant init ubuntu/focal64
 vagrant up

Has ssh, git already. Good.

No Java already. Good, can install jdk 8

 vagrant ssh
 sudo su
 apt update
 apt install openjdk-8-jre-headless
 
 
Make share to localindices on host
 config.vm.synced_folder "/home/nielserik/gitprojects/localindices", "/vagrant_data/localindices"
 
 
Install MySQL 8:

  From https://dev.mysql.com/downloads/repo/apt/
   download mysql-apt-config_0.8.24-1_all.deb
   
  in /vagrant do
   sudo dpkg -i mysql-apt-config_0.8.24-1_all.deb     (select MySQL Server & Cluster, mysql-8.0)
   
   sudo apt-get update 
   apt-get install mysql-server
       no root password 

       Also:
     │ MySQL 8 uses a new authentication based on improved SHA256-based password methods. It is recommended that all new MySQL Server installations use this method going  │ 
     │ forward. This new authentication plugin requires new versions of connectors and clients, with support for this new authentication method (caching_sha2_password).   │ 
     │ Currently MySQL 8 Connectors and community drivers built with libmysqlclient21 support this new method. Clients built with older versions of libmysqlclient may     │ 
     │ not be able to connect to the new server.                                                                                                                           │ 
     │                                                                                                                                                                     │ 
     │ To retain compatibility with older client software, the default authentication plugin can be set to the legacy value (mysql_native_password) This should only be    │ 
     │ done if required third-party software has not been updated to work with the new authentication method. The change will be written to the file                       │ 
     │ /etc/mysql/mysql.conf.d/default-auth-override.cnf                                                                                                                   │ 
     │                                                                                                                                                                     │ 
     │ After installation, the default can be changed by setting the default_authentication_plugin server setting.            
   
       retain legacy auth
       
       
   sudo su
   mysql   
    create database localindices;
    
    
    
     (readme was: grant all privileges on localindices.* to 'localidxadm'@'localhost' 
                 identified by 'localidxadmpass';
     now: 
       CREATE USER 'localidxadm'@'localhost' IDENTIFIED BY 'localidxadmpass';
       GRANT ALL PRIVILEGES ON localindices.* TO 'localidxadm'@'localhost';             
  
     cd /vagrant_data/localindices
     
      mysql -u localidxadm -plocalidxadmpass localindices <sql/schema.v2.8-with-sample-data.sql 
      mysql -u localidxadm -plocalidxadmpass localindices <sql/v2.9/2016-05-03.sql
      mysql -u localidxadm -plocalidxadmpass localindices <sql/v2.10/2016-07-04.sql
      mysql -u localidxadm -plocalidxadmpass localindices <sql/v2.11/2016-07-15.sql
      mysql -u localidxadm -plocalidxadmpass localindices <sql/v2.11/v2.11-data.sql
      mysql -u localidxadm -plocalidxadmpass localindices <sql/v2.12/2020-04-01.sql
      mysql -u localidxadm -plocalidxadmpass localindices <sql/v2.13/2020-04-15.sql
      mysql -u localidxadm -plocalidxadmpass localindices <sql/v2.14/2020-04-14.sql


    From the yaml file:
    
       apt-get install python-apt
       apt-get install apt-transport-https
       apt-get install ca-certificates
 
       but "no candidate" for tomcat8, so
       
       apt update
       apt-cache search tomcat
       
       Finds tomcat9, so
       
       apt install tomcat9
       apt install tomcat9-admin
       
       From localindices/doc/dev-notes.md  (but for tomcat9): 
         Add the following lines into /etc/tomcat9/tomcat-users.xml to give access to the Tomcat admin tool:
         <role rolename="manager-gui"/>
         <user username="admin" password="tc3636" roles="manager-gui"/>
 
         $ sudo /usr/sbin/service tomcat9 restart
         
       And more from that doc, adapted:
       
         mkdir -p /var/log/masterkey/harvester
         chown tomcat:tomcat /var/log/masterkey/harvester
         sudo mkdir -p /etc/masterkey/harvester
         sudo ln -s /vagrant_data/localindices/harvester/target/harvester/WEB-INF/harvester.properties /etc/masterkey/harvester/harvester.properties
         sudo mkdir -p /usr/share/masterkey
         
         sudo ln -s /vagrant_data/localindices/harvester/target/harvester /usr/share/masterkey/harvester
         sudo ln -s /vagrant_data/localindices/etc/harvester-context.xml /etc/tomcat9/Catalina/localhost/harvester.xml
         sudo ln -s /vagrant_data/localindices/harvester-admin/target/harvester-admin /usr/share/masterkey/harvester-admin
         sudo ln -s /vagrant_data/localindices/etc/harvester-admin-context.xml /etc/tomcat9/Catalina/localhost/harvester-admin.xml
         
        
 

Using modified version of deploy-dev.yml, doing this stuff manually  

See old deploy-dev-old.yml and deploy-dev-new.yml 


vagrant up,   yml file without localindices db setup, and then afterwards: 
 
        
-- Access to writing logs:


sudo mkdir -p /etc/systemd/system/tomcat9.service.d
echo -e "[Service]\nReadWritePaths=/var/log/masterkey" | sudo tee /etc/systemd/system/tomcat9.service.d/logging-allow.conf
sudo systemctl daemon-reload    # necessary?
sudo /usr/sbin/service tomcat9 restart
         

-- Logging into mysql to create localindices
$ cd /vagrant

$ mysql


CREATE DATABASE localindices;

CREATE USER 'localidxadm'@'localhost' IDENTIFIED BY 'localidxadmpass';

GRANT ALL PRIVILEGES ON localindices.* TO 'localidxadm'@'localhost';             
  
     
mysql -u localidxadm -plocalidxadmpass localindices <sql/schema.v2.8-with-sample-data.sql 

mysql -u localidxadm -plocalidxadmpass localindices <sql/v2.9/2016-05-03.sql

mysql -u localidxadm -plocalidxadmpass localindices <sql/v2.10/2016-07-04.sql

mysql -u localidxadm -plocalidxadmpass localindices <sql/v2.11/2016-07-15.sql

mysql -u localidxadm -plocalidxadmpass localindices <sql/v2.11/v2.11-data.sql

mysql -u localidxadm -plocalidxadmpass localindices <sql/v2.12/2020-04-01.sql

mysql -u localidxadm -plocalidxadmpass localindices <sql/v2.13/2020-04-15.sql

mysql -u localidxadm -plocalidxadmpass localindices <sql/v2.14/2020-04-14.sql


sudo /usr/sbin/service tomcat9 restart
      
         
         
