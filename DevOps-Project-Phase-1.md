# DevOps Project Phase 1 - Deploy on EC2

  ![Project-Phase1!](Images/Phase1/project-phase-1.jpg)

We know how to use work with each and Git, Jenkins independently. What if you want to collaborate these two? that is where Simple DevOps project helps you. Follow below steps if you are a new guy to DevOps. You love it.

## Follow this article in **[YouTube](https://www.youtube.com/watch?v=Z9G5stlXoyg)**

# A. Install Jenkins on AWS EC2

1. Launch Amazon Linux
  With internet access
  Security Group with Port '8080' open for internet
  ![Security-group!](Images/Phase1/jenkins-server-security-group.jpg)

2. connect to the Amazon Linux ec2 machine via putty

    ![connect-to-linux-ec2!](Images/Phase1/connect-to-linux-ec2-machine.jpg)

3. sudo yum update

4. Download and install Jenkins

    Add the Jenkins repo using the following command:
    sudo wget -O /etc/yum.repos.d/jenkins.repo <https://pkg.jenkins.io/redhat-stable/jenkins.repo>
![download-jenkins-repo!](Images/Phase1/download-jenkins-repo.jpg)

5. Import a key file from Jenkins-CI to enable installation from the package:
    sudo rpm --import <https://pkg.jenkins.io/redhat-stable/jenkins.io.key>
    - sudo yum upgrade
6. Install Jenkins using the following commands:
    1. sudo amazon-linux-extras install epel -y
    2. sudo yum update -y
    3. sudo yum install jenkins java-1.8.0-openjdk-devel -y
    4. sudo systemctl daemon-reload
7. Start Jenkins as a service:
      - sudo systemctl start jenkins
      - service jenkins start
8. You can check the status of the Jenkins service using the command:
      - sudo systemctl status jenkins

    ![check-jenkins-status!](Images/Phase1/check-jenkins-status.jpg)
9. Configure Jenkins
    Jenkins is now installed and running on your EC2 instance. To configure Jenkins:
    1. Connect to <<<<<<<<<<<<<<<http://<your_server_public_DNS>:8080>>>>>>>>>>>>>>> from your browser. You will be able to access Jenkins through its management interface:
    ![connect-public-ip!](Images/Phase1/connect-public-ip+port-8080.jpg)
      It displays the below interface:
      ![jenkins-interface!](Images/Phase1/jenkins-interface.jpg)
      - As prompted, enter the password found in /var/lib/jenkins/secrets/initialAdminPassword
      - Use the following command to display this password:
      - sudo cat /var/lib/jenkins/secrets/initialAdminPassword
      - The Jenkins installation script directs you to the Customize Jenkins page. Click Install suggested plugins.
      ![customize-jenkins!](Images/Phase1/customize-jenkins.jpg)
      - Once the installation is complete, Create First Admin User, click Save and Continue.
      ![create-admin-user!](Images/Phase1/create-admin-user.jpg)
      - Instance Configuration
      ![instance-config!](Images/Phase1/instance-config.jpg)
      - Jenkins is ready
      ![jenkins-ready!](Images/Phase1/jenkins-ready.jpg)
      - Welcome to Jenkins
      ![welcome-jenkins!](Images/Phase1/welcome-jenkins.jpg)

# B. Install & configure Maven build tool on Jenkins

## Install Maven on Jenkins

### 1. Creating maven directory under /opt

 mkdir /opt/maven
 cd /opt/maven
 ![create-maven-dir!](Images/Phase1/create-maven-directory.jpg)

### 2. Download maven packages <https://maven.apache.org/download.cgi> onto Jenkins server. In this case, I am using /opt/maven as my installation directory

downloading maven version 3.8.4

wget <https://dlcdn.apache.org/maven/maven-3/3.8.4/binaries/apache-maven-3.8.4-bin.tar.gz>
![download-maven!](Images/Phase1/download-maven.jpg)

![downloaded-maven!](Images/Phase1/downloaded-maven.jpg)

### 3. Extract the maven file

tar -xvzf apache-maven-3.8.4-bin.tar.gz
![extract-maven-file!](Images/Phase1/extract-maven-file.jpg)

### 4. Setup M2_HOME and M2 paths in .bash_profile of the user and add these to the path variable

![setup-bash-profile!](Images/Phase1/setup-bash-profile.jpg)

echo $PATH
![echo-PATH!](Images/Phase1/echo-PATH.jpg)

### 5. logoff and login to check maven version

mvn --version
![mvn-version!](Images/Phase1/mvn-version.jpg)

So far we have completed the installation of maven software to support maven plugin on the jenkins console. Let's jump onto Jenkins to complete the remaining steps.

### 6. Setup maven on Jenkins console

#### Install maven plugin without restart

- Manage Jenkins > Jenkins Plugins > available > Maven Invoker
- Manage Jenkins > Jenkins Plugins > available > Maven Integration
![install-maven-plugin!](Images/Phase1/install-maven-plugin.jpg)

#### Configure maven path

### 7. Configure Git pulgin on Jenkins

#### Install Git on Jenkins server

yum install git -y

#### Setup Git on jenkins console

- Install git plugin without restart

  - Manage Jenkins > Jenkins Plugins > available > github

- Configure git path

  - Manage Jenkins > Global Tool Configuration > git

### Part-01 : Adding steps for Integration

### Steps to create Maven Jenkin job

1. Login to Jenkins console
2. Create *Jenkins job*, Fill the following details,
   - *Source Code Management:*
      - Repository: `https://github.com/mchicha/DevOps-Final-Project.git`
      - Branches to build : `*/master`
      ![maven-job-scm!](Images/Phase1/maven-job-scm.jpg)
   - *Build:*
     - Root POM:`pom.xml`
     - Goals and options : `clean install package`
     ![maven-job-build!](Images/Phase1/maven-job-build.jpg)
   - Console Output
     ![console-output!](Images/Phase1/console-output.jpg)

### Steps to create Pipeline Jenkin job

1. Login to Jenkins console
2. Create *Pipeline Jenkins job*, Fill the following details,

- *Pipeline*:
  - Pipeline script
  - Script:

```
    pipeline{
        agent any
        stages {
            stage("git"){
                steps{
                    git branch: 'master', url: 'https://github.com/mchicha/DevOps-Final-Project.git'
                }
            }
        }
    }

```

  ![pipeline-script!](Images/Phase1/pipeline-script.jpg)

# C.Tomcat installation on EC2 instance

1. Launch Amazon Redhat ec2 Machine to install Apache
    Define Security Group (give internet access to connect to the server)

    HTTP 8080 ----> connect to the server via the web
  ![tomcat-server-security-group!](Images/Phase1/tomcat-server-security-group.jpg)

2. Connect to the Tomcat server via putty
  ![connect-to-tomcat-server!](Images/Phase1/connect-to-tomcat-server.jpg)

3. sudo su -

4. Install Java

    yum install java-1.8*

    ![install-java!](Images/Phase1/install-java.jpg)

5. Check the java version

    java -version  
    ![java-version!](Images/Phase1/java-version.jpg)

6. sudo yum update

7. Install wget

    sudo yum install wget

8. cd /opt

    ![cd-opt!](Images/Phase1/cd-opt.jpg)

9. Download packages(find the packages to download by searching on google tomcat - download packages)

    Copy link address:
    wget <https://dlcdn.apache.org/tomcat/tomcat-10/v10.0.14/bin/apache-tomcat-10.0.14.tar.gz>

    ![download-tomcat-packages!](Images/Phase1/download-tomcat-packages.jpg)

10. Open tar file

    tar -zvxf apache-tomcat-10.0.14.tar.gz
    
    ![open-tar-file!](Images/Phase1/open-tar-file.jpg)

11. cd apache-tomcat-10.0.14

    ![cd-apache!](Images/Phase1/cd-apache.jpg)

12. cd bin/

    ![cd-bin!](Images/Phase1/cd-bin.jpg)

13. Give permission to run the startup.sh file

    chmod +x startup.sh

14. Give permission to run the shutdown.sh file

    chmod +x shutdown.sh

15. Create link files for tomcat startup.sh and shutdown.sh

    ln -s /opt/apache-tomcat-10.0.14/bin/startup.sh /usr/local/bin/tomcatup

    ln -s /opt/apache-tomcat-10.0.14/bin/shutdown.sh /usr/local/bin/tomcatdown
  
16. Run tomcatup

    ![run-tomcatup!](Images/Phase1/run-tomcatup.jpg)

17. Access tomcat application from browser on port 8080

    <<http://<Public_IP>:8080>>

    ![access-tomcat-application!](Images/Phase1/access-tomcat-application.jpg)

### Part-02: Adding Deployment Steps

### Jenkins server

1. sudo su -
2. cd /var/lib/jenkins
3. ls

    ![jenkins-server-var-lib-jenkins-ls!](Images/Phase1/jenkins-server-var-lib-jenkins-ls.jpg)

4. cd workspace

    ![cd-workspace!](Images/Phase1/cd-workspace.jpg)

5. cd pipeline-job-with-maven-build

    ![pipeline-job-maven-build!](Images/Phase1/pipeline-job-maven-build.jpg)

6. cd webapp

    ![cd-webapp!](Images/Phase1/cd-webapp.jpg)

7. Install tree command

- sudo su -
- yum install tree

8. rum command tree

    ![tree!](Images/Phase1/tree.jpg)

### Tomcat server

1. cd /opt
2. ls
3. cd apache-tomcat-10.0.14

    ![cd-apache-tomcat!](Images/Phase1/cd-apache-tomcat.jpg)

### Jenkins server

1. We have to install SSH Agent Plugin on Jenkins in order to transfer files from the jenkins server
to the tomcat server

   ![ssh-agent-plugin!](Images/Phase1/ssh-agent-plugin.jpg)

2. Write pipeline script with ssh agent to deploy on the tomcat server

    Here is the pipeline script from which we will add the deployment code:

```
    pipeline{
        agent any
        environment{
            PATH = "/opt/maven/apache-maven-3.8.4/bin:$PATH"
        }
        stages {
            stage("clone code"){
                steps{
                    git branch: 'phase-1-deploy-on-ec2', url: 'https://github.com/mchicha/DevOps-Final-Project.git'
                }
            }
            stage("build code"){
                steps{
                    sh "mvn clean install"
                }
            }
        }
    }

```

![pipeline-job-with-deploy!](Images/Phase1/pipeline-job-with-deploy.jpg)

3. Click Pipeline Synthax
![snippet-generator!](Images/Phase1/snippet-generator.jpg)

4. Choose in the Sample Step: SSH Agent
![choose-ssh-agent!](Images/Phase1/choose-ssh-agent.jpg)

5. Click Add Jenkins Credentials

    ![add-jenkins-credentials!](Images/Phase1/add-jenkins-credentials.jpg)

6. It displays a Jenkins Credentials Provider window
![window-Jenkins-credentials!](Images/Phase1/window-Jenkins-credentials.jpg)

7. Fill the data
   - For Kind: Choose SSH Username with private key
   - For ID: Enter deploy_user
   - For Description: Enter deploy_user
   - For Username: Enter ec2-user
   - For Private Key: Selected Enter directly
   - Click the Add Button and paste the tomcat-server.pem key
   - Click the Add button on the left bottom of the window

    ![fill-window-jenkins-credentials!](Images/Phase1/fill-window-jenkins-credentials.jpg)

8. Choose ec2-user(deploy_user)

    ![choose-ec2-user!](Images/Phase1/choose-ec2-user.jpg)

9. Click on the Generate Pipeline Script

    ![click-generate-pipeline-script!](Images/Phase1/click-generate-pipeline-script.jpg)

10. Copy the sshagent script to the pipeline script adding a stage:

  ```
  sshagent(['deploy_user']) {
      // some block
  }
  ```  
  ![add-sshagent-script-to-pipeline-script!](Images/Phase1/add-sshagent-script-to-pipeline-script.jpg)

11. Add in the sshagent segment the command scp to copy the webapp.war file from the jenkins server to the tomcat server

    ![from-address!](Images/Phase1/from-address.jpg)

  - Here is the synthax of the scp command:

    scp <src_file> username@IP:<dest_path>
    
    where IP is the IP of the tomcat server
    
    and <dest_path> is /opt/apache-tomcat-10.0.14/webapps

    ![dest-path!](Images/Phase1/dest-path.jpg)

    scp webapp/target/webapp.war ec2-user@54.215.70.238:/opt/apache-tomcat-10.0.14/webapps

12. Finally add the next command to the pipeline script:

    sh "scp webapp/target/webapp.war ec2-user@54.215.70.238:/opt/apache-tomcat-10.0.14/webapps"

    sh for shell command

13. Add the attribute StrictHostKeyChecking=no in the scp command:

    ![add-attribute-to-scp-command!](Images/Phase1/add-attribute-to-scp-command.jpg)

    sh "scp -o StrictHostKeyChecking=no webapp/target/webapp.war ec2-user@54.215.70.238:/opt/apache-tomcat-10.0.14/webapps"

14. Change the owner and group permissions of the /opt directory and sub-directories of the tomcat server to ec2-user

  - sudo chown -R ec2-user:ec2-user /opt

    ![chown-opt-dir!](Images/Phase1/chown-opt-dir.jpg)

15. Finally pipeline script

```
  pipeline{
      agent any
      environment{
          PATH = "/opt/maven/apache-maven-3.8.4/bin:$PATH"
      }
      stages {
          stage("clone code"){
              steps{
                  git branch: 'phase-1-deploy-on-ec2', url: 'https://github.com/mchicha/DevOps-Final-Project.git'
              }
          }
          stage("build code"){
              steps{
                  sh "mvn clean install"
              }
          }
          stage("deploy code"){
              steps{
                  sshagent(['deploy_user']) {
                      sh "scp -o StrictHostKeyChecking=no webapp/target/webapp.war ec2-user@54.215.70.238:/opt/apache-tomcat-10.0.14/webapps"
                  }
              }
          }
      }
  }

```
  ![finally-pipeline-script!](Images/Phase1/finally-pipeline-script.jpg)

16. Run the pipeline job on the jenkins server
  
  ![pipeline-job-success!](Images/Phase1/pipeline-job-success.jpg)

17. Browser on the tomcat server
  
  ![browser-on-tomcat-server!](Images/Phase1/browser-on-tomcat-server.jpg)

18. Browser on the tomcat server after doing changes in the index.jsp file of the the webapp

  ![browser-on-tomcat-server-after-changes!](Images/Phase1/browser-on-tomcat-server-after-changes.jpg)


### Part-03 : Continuous Integration & Continuous Deployment (CI/CD)

Now job is running fine but to make this as Continuous Integration and Continuous Deployment Tod do that go back and modify job as below.

- Build Triggers
  - Poll SCM
    - schedule `*/2 * * * *`

Save the job and modify the code in GitHub. Then you could see your job get trigger a build without any manual intervention.
