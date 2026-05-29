# Deploy on a Tomcat server
# *Jenkins Job name:* `Deploy_on_Tomcat_Server`

### Pre-requisites

1. Jenkins server 
2. Tomcat Server 

### Adding Deployment steps

1. Install 'deploy to container' plugin. This plugin needs to deploy on tomcat server. 

  - Install 'deploy to container' plugin without restart  
    - `Manage Jenkins ⚙️` > `Plugins` > `Available plugins` > `deploy to container`
 
2. Jenkins should need access to the tomcat server to deploy build artifacts. setup credentials to enable this process. use credentials option on Jenkins home page.

- setup credentials
  - `Manage Jenkins ⚙️` > `Credentials` > `➕ Add credentials`
    - Username	: `deployer`
    - Password : `deployer`
    - id      :  `deployer`
    - Description: `user to deploy on tomcat vm`

### Steps to create "Deploy_on_Tomcat_Server" Jenkin job

 #### From Jenkins home page select "New Item"
   - Enter an item name: `Deploy_on_Tomcat_Server`
     - Copy from existing job: `My_First_Maven_Build`
     
   - *Source Code Management:*
      - Repository: `https://github.com/ngostal2019/Resume-template-generator.git`
      - Branches to build : `*/main`  
   - *Poll SCM* :
      - `* * * *`

   - *Build:*
     - Root POM:`pom.xml`
     - Goals and options: `clean install package`

 - *Post-build Actions*
   - Choose Deploy war/ear to container
      - WAR/EAR files : `target/r*.war`
      - Containers : `Tomcat 9.x`
         - Credentials: `deployer` (user created above)
         - Tomcat URL : `http://<PUBLIC_IP>:8080/`

Save and run the job now.
