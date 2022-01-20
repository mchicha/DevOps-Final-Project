# DevOps Project Phase 2 - Deploy on Docker

  ![Project-Phase2!](Images/Phase2/project-phase-2.jpg)

1. Launch an EC2 instance for Docker server
   
   With internet access

   Security Group with Port '8090' open for internet
   ![Security-group!](Images/Phase2/docker-server-security-group.jpg)

2. connect to the Docker server(Amazon Linux ec2 machine) via putty

    ![connect-to-docker-server!](Images/Phase2/connect-to-docker-server.jpg)

3. Install docker on EC2 instance
    - sudo su -
    - yum install docker -y

    ![install-docker!](Images/Phase2/install-docker.jpg)

    - docker --version

    ![docker-version!](Images/Phase2/docker-version.jpg)

4. Start docker services
    - service docker start
    - service docker status

    ![service-docker-start!](Images/Phase2/service-docker-start.jpg)

5. Create a user called dockeradmin
    - useradd dockeradmin
    - passwd dockeradmin

    ![create-a-user!](Images/Phase2/create-a-user.jpg)

6. Add a user to docker group to manage docker
    - usermod -aG docker dockeradmin

    ![add-user-to-docker-group!](Images/Phase2/add-user-to-docker-group.jpg)

7. Write the Dockerfile file under /opt/docker
    - cd /opt
    - mkdir docker

    ![create-docker-dir!](Images/Phase2/create-docker-dir.jpg)

8. Create a Dockerfile file

    ```
        FROM amazonlinux:2
        RUN yum install java -y
        RUN mkdir /opt/tomcat/
        WORKDIR /opt/tomcat
        ADD https://dlcdn.apache.org/tomcat/tomcat-10/v10.0.14/bin/apache-tomcat-10.0.14.tar.gz /opt/tomcat
        RUN tar xvfz apache*.tar.gz
        RUN mv apache-tomcat-10.0.14/* /opt/tomcat

        COPY ./webapp.war /usr/local/tomcat/webapps

    ```
    ![create-dockerfile!](Images/Phase2/create-dockerfile.jpg)

9. Login to Jenkins Console and add Docker server to execute commands from Jenkins
    - Manage Jenkins --> Configure system --> Publish over SSH --> add Docker server and credentials