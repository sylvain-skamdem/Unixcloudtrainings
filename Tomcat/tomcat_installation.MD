# Tomcat installation on a local Linux VM or an Amazon EC2 VM instance

## Pre-requisites

### Java installation

1. EC2 instance/Local VM with Java v25.x.y with JAVA_HOME path set.

    For a definition about Apache Tomcat, please read on [Wikipedia](https://en.wikipedia.org/wiki/Apache_Tomcat)

    Tomcat requires Java in order to work properly and we will install the same version as per what we have on our [pom.xml](https://github.com/ngostal2019/Resume-template-generator/blob/main/pom.xml#L21) file on our project.

    For Debian-based systems

    ```sh
    sudo apt update
    sudo apt dist-upgrade -y
    sudo apt install openjdk-25-jre-headless -y

    # Find the java binary Path to set JAVA_HOME and chose one that matches /usr/lib/jvm/java-xx-openjdk-amd64
    find / -name "*openjdk*" -type d 2>/dev/null

    # Set the path now
    vim ~/.profile

    # Then add the JAVA_HOME variable with the content of your Java version at the bottom of the file
    JAVA_HOME="/usr/lib/jvm/java-25-openjdk-amd64"
    PATH=$JAVA_HOME/bin:$PATH
    ```

### Install Apache Tomcat

1. Download tomcat packages (preferrably the tar.gz file) from the [Tomcat](https://tomcat.apache.org/download-11.cgi) website onto /opt on the instance

   ```sh 
   # Download, untar the tomcat gz file and delete it
   cd /tpm
   wget https://dlcdn.apache.org/tomcat/tomcat-11/v11.0.22/bin/apache-tomcat-11.0.22.tar.gz
   sudo tar xvzf /opt/apache-tomcat-11.0.22.tar.gz -C /opt
   rm -rf apache-tomcat-11.0.22.tar.gz
   ```

1. Change ownership and give executing permissions to the /opt/tomcat folder. 

   ```sh
   # Rename the folder apache-tomcat-11.0.22 for easier management
   cd /opt
   sudo mv apache-tomcat-11.0.22 tomcat
   # Create a tomcat local Linux user
   sudo useradd -r -m -U -d /opt/tomcat -s /bin/false tomcat
   # Change ownership and permision
   sudo chown -R tomcat:tomcat /opt/tomcat
   sudo chmod -R 755 /opt/tomcat
   ```

1. Set Tomcat to run as a system service 

   ```sh
   sudo nano /etc/systemd/system/tomcat.service

   # Add the below content inside the tomcat.service file
   [Unit]
   Description=Apache Tomcat 11 Web Application Container
   After=network.target

   [Service]
   Type=forking

   User=tomcat
   Group=tomcat

   Environment="JAVA_HOME=/usr/lib/jvm/java-21-openjdk-amd64"   # Update path
   Environment="CATALINA_PID=/opt/tomcat/temp/tomcat.pid"
   Environment="CATALINA_HOME=/opt/tomcat"
   Environment="CATALINA_BASE=/opt/tomcat"
   Environment="CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC"

   ExecStart=/opt/tomcat/bin/startup.sh
   ExecStop=/opt/tomcat/bin/shutdown.sh

   [Install]
   WantedBy=multi-user.target

   # Save the file content and start the service
   sudo systemctl daemon-reload
   sudo systemctl enable tomcat
   sudo systemctl start tomcat
   sudo systemctl status tomcat
   ```

   #### Check point 1

   Access tomcat application from browser on prot 8080  
   - http://<Public_IP>:8080

   **OPTIONAL when on different machines**: Using unique ports for each application is a best practice in an environment. But tomcat and Jenkins runs on ports number 8080. Hence you should consider changing tomcat port number to another number (8090 for instance) if both are installed on the same system. If that's the case, please change port number in conf/server.xml file under tomcat home

   ```sh
   cd /opt/tomcat/conf
   # update port number in the "connecter port" field in server.xml and restart tomcat after configuration update
   sudo systemctl restart tomcat.service
   ```

   #### Check point 2

   Access tomcat application from browser on prot 8090 if port number was changed from 8080
   - http://<Public_IP>:8090

1. Now application is accessible on port 8090/8080.

   Tomcat application doesn't allow to login from browser when we click on the `Manager App` button on the right side of welcome page. So, changing the default parameter in context.xml does address this issue

   ```sh
   #search for context.xml
   find / -name context.xml
   ```

1. Above command gives a few context.xml files.
   In the `./webapps/manager/META-INF/context.xml` file, comment (<!-- & -->) `Valve ClassName` field on files which are under webapp directory.

   ```sh
   <!-- <Valve className="org.apache.catalina.valves.RemoteCIDRValve"
   allow="127.0.0.0/8,::1/128" /> -->
   ```

   After that restart tomcat services to effect these changes

   ```sh
   sudo systemctl restart tomcat.service
   ```

1. Update users information in the `tomcat-users.xml` file

    Go to the tomcat home directory and Add below users to conf/tomcat-user.xml file

      ```sh
      <role rolename="manager-gui"/>
      <role rolename="manager-script"/>
      <role rolename="manager-jmx"/>
      <role rolename="manager-status"/>
      <user username="admin" password="admin" roles="manager-gui, manager-script, manager-jmx, manager-status"/>
      <user username="deployer" password="deployer" roles="manager-script"/>
      <user username="tomcat" password="s3cret" roles="manager-gui"/>
      ```

1. Restart serivce and try to login to tomcat application from the browser.

   This time it should be Successful and you may now use the tomcat user with his password to login.

   > [!IMPORTANT]
   >> If for any reasons, you're still denied access with a 403 HTTP code, please clear your browser cookies and cache data from the last hours before attempting to login to tomcat.

1. Now upload your `*.war` file to the server and deploy it

   Once deployed, a `*.war` file will be created under your `$CATALINA_HOME/webapps` folder aong with the unarchived folder name of the same `*.war` artifact.

   > We just scratched the surface
   >> Good job for making it so far :rocket:
