# Install Jenkins on AWS EC2

Jenkins is a self-contained Java-based program, ready to run out-of-the-box, with packages for Windows, Mac OS X and other Unix-like operating systems. As an extensible automation server, Jenkins can be used as a simple CI server or turned into the continuous delivery hub for any project.

### Prerequisites
1. Ubuntu EC2 Instance 
   - With Internet Access
   - Security Group with Port `8080` open for internet

1. Java v25.x.x.x 

## Install Java
1. We will be using open java for our demo, Set your version
   ```sh
   sudo apt-get update
   sudo apt-get install -y lsb-release wget tar gnupg2 ca-certificates git
   sudo mkdir -p /etc/apt/keyrings
   wget -qO - https://packages.adoptium.net/artifactory/api/gpg/key/public | sudo gpg --dearmor -o /etc/apt/keyrings/adoptium.gpg
   echo "deb [signed-by=/etc/apt/keyrings/adoptium.gpg] https://packages.adoptium.net/artifactory/deb $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/adoptium.list
   sudo apt-get update
   sudo apt-get install -y temurin-25-jdk
   ```

1. Confirm Java Version and set the java home momentally
   ```sh
   java -version
   find /usr/lib/jvm -name "*-25-jdk*"
   JAVA_HOME=/usr/lib/jvm/temurin-25-jdk-amd64
   PATH=$PATH:$JAVA_HOME/bin
   ```
   
   To set it permanently create a *.sh in the /etc/profile.d folde
   ```sh
   sudo vi /etc/profile.d/java_maven.sh
   # If for some reason you don't find the PATH variable in this file on-premise, please add these lines along with the JAVA_HOME variable you're trying to setup.
   JAVA_HOME=/usr/lib/jvm/temurin-25-jdk-amd64
   PATH=$PATH:$JAVA_HOME/bin
   export PATH

   - To load your recent modifications, run
   
   ```sh
   [root@~]# source /etc/profile.d/java_maven.sh
   sudo chmod 644 /etc/profile.d/java_maven.sh

   Then
   [root@~]# java -version
   
   The output should be something similar like this one below:

   openjdk 25.0.3 2026-04-21 LTS
   OpenJDK Runtime Environment Temurin-25.0.3+9 (build 25.0.3+9-LTS)
   OpenJDK 64-Bit Server VM Temurin-25.0.3+9 (build 25.0.3+9-LTS, mixed mode, sharing)
   ```

## Install Jenkins
 You can install jenkins by setting up the repo so that we can update it easily in the future.
1. Get the latest version of jenkins from https://pkg.jenkins.io/debian-stable/ and install
   ```sh
   sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2026.key
   echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
   sudo apt-get update
   sudo apt-get install -y fontconfig
   sudo apt-get install -y jenkins
   ```


   ### Start Jenkins
   ```sh
   # Start jenkins service
   sudo service jenkins start OR sudo systemctl start jenkins

   # Setup Jenkins to start at boot,
   sudo chkconfig jenkins on OR sudo systemctl enable jenkins
   ```

   ### Accessing Jenkins
   By default jenkins runs at port `8080`, You can access jenkins at
   ```sh
   http://YOUR-SERVER-PUBLIC-IP:8080
   ```
## Configure Jenkins UI
- The default Username is `admin`
- Grab the default password 
- Password Location:`/var/lib/jenkins/secrets/initialAdminPassword`
- `Choose` the recommended Plugins Installation; _We can do it later_
- Change admin password
   - `Admin` > `Configure` > `Password`
- Configure `java` path
  - `Manage Jenkins` > `Tools` > `JDK`  
- Create another admin user id (Optional but recommended)

## Test Jenkins Jobs
1. Create “new item”
1. Enter an item name – `My-First-Project`
   - Chose `Freestyle` project
1. Under the Build section
	Execute shell: echo "Welcome to Jenkins Demo"
1. Save your job 
1. Build job
1. Check "console output"

