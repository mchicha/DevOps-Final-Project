# DevOps Project Phase 2 - Deploy on Docker

  ![Project-Phase2!](Images/Phase2/project-phase-2.jpg)

1. Launch an EC2 instance for Docker server
   
   With internet access

   Security Group with Port '8080' open for internet
   ![Security-group!](Images/Phase2/docker-server-security-group.jpg)

2. Connect to the Docker server(Amazon Linux ec2 machine) via putty

    ![connect-to-docker-server!](Images/Phase2/connect-to-docker-server.jpg)

3. Chnage hostname of the ec2 machine to docker-server
    - sudo su -
    - hostname docker-server
    - sudo su -

    ![change-hostname!](Images/Phase2/change-hostname.jpg)
    
4. Install docker on EC2 instance
    - yum install docker -y

    ![install-docker!](Images/Phase2/install-docker.jpg)

    - docker --version

    ![docker-version!](Images/Phase2/docker-version.jpg)

5. Start docker services
    - service docker start
    - service docker status

    ![service-docker-start!](Images/Phase2/service-docker-start.jpg)

6. Create a user called dockeradmin
    - useradd dockeradmin
    - passwd dockeradmin

    ![create-a-user!](Images/Phase2/create-a-user.jpg)

7. Add a user to docker group to manage docker
    - usermod -aG docker dockeradmin

    ![add-user-to-docker-group!](Images/Phase2/add-user-to-docker-group.jpg)

8. Write the Dockerfile file under /home/dockeradmin
    - cd /home
    - ls
    - cd dockeradmin


    ![cd-home-dockeradmin!](Images/Phase2/cd-home-dockeradmin.jpg)

9. Create a Dockerfile file
    - sudo nano Dockerfile
    ```
        # Pull tomcat image
        FROM tomcat:8.0

        # Copy webapp.war to tomcat
        COPY ./webapp.war /usr/local//tomcat/webapps
        
    ```
    ![create-dockerfile!](Images/Phase2/create-dockerfile.jpg)

10. Login to Jenkins Console and add Docker server to execute commands from Jenkins
    - Manage Jenkins --> Configure system --> Publish over SSH --> add Docker server and credentials
    - Set for Name: docker-server
    - Set for Hostname: Private IP of the docker-server
    - Set for Username: dockeradmin
    - Set for Password: *******

    ![add-credentials!](Images/Phase2/add-credentials.jpg)

11. Edit the sshd_config file
    - sudo su -
    - nano /etc/ssh/sshd_config
    - set PasswordAuthentication yes

    ![modify-sshd_config-file!](Images/Phase2/modify-sshd_config-file.jpg)

    - Run command: service sshd restart
    
    or 

    - Run command: service sshd reload

    ![service-sshd-restart!](Images/Phase2/service-sshd-restart.jpg)

12. Create a new job: deploy-on-docker
    ### Part: Source code Management
    - Repository URL: https://github.com/mchicha/DevOps-Final-Project.git
    - Branch Specifier (blank for 'any'): */phase-2-deploy-on-docker

    ![source-code-management!](Images/Phase2/source-code-management.jpg)

    ### Part: Build
    - Root POM: pom.xml
    - Goals and options: clean install package

    ![part-build!](Images/Phase2/part-build.jpg)

    ### Post build step
    - Click on Add post build step
    - Select: Send files or execute commands over SSH
    - Set for Name: docker-server
    - Set for Source files: webapp/target/*.war
    - Set for Remove prefix: webapp/target
    - Set for Remote directory: .
    - Exec command: 
```
        docker stop tomcat-server-ctr;
        docker rm -f tomcat-server-ctr;
        docker rmi -f tomcat-server-v8;
        docker build -t tomcat-server-v8 -f Dockerfile . ;
        docker run -d --name tomcat-server-ctr -p 8080:8080 tomcat-server-v8

```
    ![send-files-over-ssh!](Images/Phase2/send-files-over-ssh.jpg)

13. Check on the docker-server
    - cd /home/dockeradmin
    - docker images
    - docker ps

    ![docker-images!](Images/Phase2/docker-images.jpg)

14. Build the job: Deploy-on-docker
    - Error when build job: ERROR: Exception when publishing, exception message [Permission denied]

    ![error-when-build-job!](Images/Phase2/error-when-build-job.jpg)

15. Solution: chown -R dockeradmin:dockeradmin /home/dockeradmin

    ![chown-dockeradmin!](Images/Phase2/chown-dockeradmin.jpg)

16. Build again the job: Deploy-on-docker
    ### Console output

    ![console-output!](Images/Phase2/console-output.jpg)

17. Browser on the docker-server
    ![aws-ec2-dashboard!](Images/Phase2/aws-ec2-dashboard.jpg)

    - Public IP: 13.57.41.156
    - Port: 8080

    ![browser-on-docker-server-port-8080!](Images/Phase2/browser-on-docker-server-port-8080.jpg)

    - browse webapp: 13.57.41.156:8080/webapp

    ![browser-on-docker-server-webapp!](Images/Phase2/browser-on-docker-server-webapp.jpg)