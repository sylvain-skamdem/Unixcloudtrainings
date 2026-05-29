#  Install & configure Maven build tool on Jenkins
Maven is a code build tool which used to convert your code to an artifact. this is a widely used plugin to build in continuous integration


#### Prerequisites
1. Same Jenkins server

#### Install Maven on Jenkins
1. Download maven packages https://maven.apache.org/download.cgi onto Jenkins server. In this case, I am using /opt/maven as my installation directory
 - Link : https://maven.apache.org/download.cgi

   ```sh
   # Creating maven directory under /opt
   sudo mkdir /opt/maven
   cd /opt/maven
   # downloading maven version 3.x.x
   MAVEN_VERSION=3.9.16 # Set this to the version you want
   sudo mkdir -p /opt/maven
   wget -qO /tmp/apache-maven-${MAVEN_VERSION}-bin.tar.gz https://archive.apache.org/dist/maven/maven-3/${MAVEN_VERSION}/binaries/apache-maven-${MAVEN_VERSION}-bin.tar.gz
   sudo tar xf /tmp/apache-maven-${MAVEN_VERSION}-bin.tar.gz -C /opt/maven --strip-components=1
   sudo chown -R root:root /opt/maven
   ```
	
1. Setup M2_HOME and M2 paths in .bash_profile of the user and add these to the path variable
   ```sh
   sudo vi /etc/profile.d/java_maven.sh
   M2_HOME="/opt/maven"
   PATH=$PATH:$JAVA_HOME/bin:$M2_HOME/bin
   ```
#### Checkpoint 
1. Run the command below to load your configuration 
   ```sh
   sudo vi /etc/profile.d/java_maven.sh
  ```
1. Check maven version
   mvn --version

   So far we have completed the installation of maven software to support maven plugin on the jenkins console. 
   Let's jump onto Jenkins to complete the remaining steps. 

# Setup maven on Jenkins console
2. Install maven plugin without restart  
  - `Manage Jenkins ⚙️` > `Plugins` > `Available plugins` > `Maven Invoker`
  - `Manage Jenkins ⚙️` > `Plugins` > `Available plugins` > `Maven Integration`

3. Configure maven path
  - `Manage Jenkins ⚙️` > `Tools` > `Maven` Configure the maven path set on the Jenkins server