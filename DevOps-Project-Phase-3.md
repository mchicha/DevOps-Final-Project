# DevOps Project Phase 3 - Deploy on Docker by Ansible

  ![Project-Phase3!](Images/Phase3/project-phase-3.jpg)

1. Launch an EC2 instance for Ansible server
    uses Amazon Linux 2 AMI as for the Jenkins server and the docker server

    ![amazon-linux2-ami!](Images/Phase3/amazon-linux2-ami.jpg)

    With internet access

    Security Group with Port '8080' open for internet
    ![ansible-server-security-group!](Images/Phase3/ansible-server-security-group.jpg)

    AWS EC2 Dashboard

    ![aws-ec2-dashboard!](Images/Phase3/aws-ec2-dashboard.jpg)

 2. Connect to the docker-server via Putty
    
    Change hostname of the ec2 machine to docker-server:
    - sudo su -
    - hostname docker-server
    - sudo su -

    ![connect-to-docker-server!](Images/Phase3/connect-to-docker-server.jpg)

3. Run command: docker ps

   ![docker-ps!](Images/Phase3/docker-ps.jpg)

4. Start docker services

        . service docker start
        . service docker status

    ![service-docker-start!](Images/Phase3/service-docker-start.jpg)

5. Connect to the ansible-server via Putty

   Change hostname of the ec2 machine to ansible-server:
    
        . sudo su -
        . hostname ansible-server
        . sudo su -

    ![connect-to-ansible-server!](Images/Phase3/connect-to-ansible-server.jpg)

6. Ansible Installation on the ansible-server

    a. Install python and python-pip

        . yum install python
        . python --version

    ![install-python!](Images/Phase3/install-python.jpg)

        . yum install python-pip

    ![install-python-pip!](Images/Phase3/install-python-pip.jpg)

    b. Install ansible using pip check for version

        . pip install ansible

    ![install-ansible-1!](Images/Phase3/install-ansible-1.jpg)

    ![install-ansible-2!](Images/Phase3/install-ansible-2.jpg)

        . ansible --version

    ![ansible-version!](Images/Phase3/ansible-version.jpg)

    c. Create a new directory inside /etc/

        . mkdir /etc/ansible
    
    d. Create a user called ansadmin (on Control node and Managed host)

        . useradd ansadmin
        . passwd ansadmin

     ![useradd-ansadmin!](Images/Phase3/useradd-ansadmin.jpg)

    e. Below command grant sudo access to ansadmin user. But we strongly recommended using "visudo" command if you are aware vi or nano editor. (on Control node and Managed host)

        . visudo

        echo "ansadmin ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

    f. Ansible server used to create images and store on docker registry. Hence install docker, start docker services and add ansadmin to the docker group.

        . yum install docker

     ![install-docker!](Images/Phase3/install-docker.jpg)

     ![docker-installed!](Images/Phase3/docker-installed.jpg)

    g. Start docker services

        . service docker start
        . service docker status

     ![service-docker-start-on-ansible-server!](Images/Phase3/service-docker-start-on-ansible-server.jpg)

    h. Add user to docker group
        
        . usermod -aG docker ansadmin

    ![add-user-to-docker-group!](Images/Phase3/add-user-to-docker-group.jpg)

    i. Edit the sshd_config file as we have done in the docker-server

        . nano /etc/ssh/sshd_config
        . set PasswordAuthentication yes
        
    ![modify-sshd_config-file!](Images/Phase3/modify-sshd_config-file.jpg)

    j. Run the next command in order that the changes in the sshd_config file will work:
        . service sshd reload
    ![service-sshd-reload!](Images/Phase3/service-sshd-reload.jpg)

    k. Log in as ansadmin user on master and generate ssh key (on Control node)

        . sudo su - ansadmin

    ![login-as-ansadmin!](Images/Phase3/login-as-ansadmin.jpg)

    l. Generate ssh key

        . ssh-keygen

    ![ssh-keygen!](Images/Phase3/ssh-keygen.jpg)

    m. Run the next command:
       
        . ls -la
        . cd .ssh/
        . ls
    ![ls-la!](Images/Phase3/ls-la.jpg)

        id_rsa is the private key(if we share the private key, anyone will able to access our system)
        id-rsa.pub is the public key

        we are going to share only the public key and not the private key

        We create the keys to interact to build our connections with the docker-server. And for this we required a user that's why we created a user and we are going to share the public key to the docker-server. So we have the integration things. Now we have the setup for the ansible.

    n. Run the next command

        . exit
    ![exit-ssh!](Images/Phase3/exit-ssh.jpg)

7. Create the user ansadmin on the docker-server
    
        . sudo su -
        . useradd ansadmin
        . passwd ansadmin

    ![create-user-ansadmin-to-docker-server!](Images/Phase3/create-user-ansadmin-to-docker-server.jpg)

8. On the Ansible-server, login to the ansadmin user
        
        . su - ansadmin

9. Copy keys onto all ansible managed hosts (on Control node)

        . ssh-copy-id ansadmin@<target-server>

        where <target-server> is the Private IP of the docker-server

        Private IP = 172.31.7.60
        
        Run the next command:
        . ssh-copy-id ansadmin@172.31.7.60  
        
      ![ssh-copy-id!](Images/Phase3/ssh-copy-id.jpg)

10. Connect to the docker-server from the Ansible-server via ssh

        . ssh ansadmin<target-server>

        where <target-server> is the Private IP of the docker-server

        Private IP = 172.31.7.60

        Run the next command:
        . ssh ansadmin@172.31.7.60

    ![ssh-ansadmin!](Images/Phase3/ssh-ansadmin.jpg)

11. Run the exit command from the Ansible-server:
        
        . exit
    
    ![exit-from-ansible-server!](Images/Phase3/exit-from-ansible-server.jpg)

12. Logout from ansadmin user on the Ansible-server

        . pwd
        . ls
        . exit

    ![exit-ansadmin!](Images/Phase3/exit-ansadmin.jpg)
           
13. Validation test(check the ping request)

    a. Create an inventory file called "hosts" in the /etc/ansible directory add control node IP address in it.

        . cd /etc/ansible
        . ls
        . nano hosts(if it not existed)
        . Add the Private IP of the docker-server
        . Add localhost

    ![create-hosts-file!](Images/Phase3/create-hosts-file.jpg)

    b. Login to the ansadmin user on the Ansible-server

        . su - ansadmin

    ![login-to-ansadmin!](Images/Phase3/login-to-ansadmin.jpg)

    c. Run ansible command as ansadmin user it should be successful (Master)

        . ansible all -m ping

    ![ansible-ping!](Images/Phase3/ansible-ping.jpg)

    The local host failed because we have to add the key for the local system because we already added the key for the remote target machine that is the talker host

    d. Run the next command:

        . ssh-copy-id localhost

    ![ssh-copy-id-localhost!](Images/Phase3/ssh-copy-id-localhost.jpg)

    e. Run again the next command:

        . ansible all -m ping

    ![ansible-ping2!](Images/Phase3/ansible-ping2.jpg)

    Ansible is now able to talk with the docker-server

14. Login to Jenkins Console and add the Ansible-server to execute commands from Jenkins
        
        . Manage Jenkins --> Configure system --> Publish over SSH --> add Ansible server and credentials
        . Set for Name: ansible-server
        . Set for Hostname: Private IP of the ansible-server
        . Set for Username: ansadmin
        . Set for Password: *******

    ![add-credentials-ansible-server!](Images/Phase3/add-credentials-ansible-server.jpg)

15. Edit the file /etc/sudoers on the ansible-server

        . sudo nano /etc/sudoers
        . Add: ansadmin ALL=(ALL)  ALL

    ![edit-sudoers!](Images/Phase3/edit-sudoers.jpg)

<!-- 16. Create the directory docker in /opt

        . su - ansadmin
        . cd /opt
        . sudo mkdir docker
    ![mkdir-docker!](Images/Phase3/mkdir-docker.jpg)

17. Give full access to ansdmin user to the docker directory

        . sudo chown -R ansadmin:ansadmin /opt/docker
        . ls -l /opt
    ![sudo-chown-docker!](Images/Phase3/sudo-chown-docker.jpg) -->
    
16. Create a new job on jenkins: deploy-on-docker-using-ansible
    ### Part: Source code Management
    - Repository URL: https://github.com/mchicha/DevOps-Final-Project.git
    - Branch Specifier (blank for 'any'): */phase-3-deploy-on-docker-by-ansible

    ![source-code-management!](Images/Phase3/source-code-management.jpg)

    ### Part: Build
    - Root POM: pom.xml
    - Goals and options: clean install package

    ![part-build!](Images/Phase3/part-build.jpg)

    ### Post build step
    
        . Click on Add post build step
        . Select: Send files or execute commands over SSH
        . Set for Name: ansible-server
        . Set for Source files: webapp/target/*.war
        . Set for Remove prefix: webapp/target
        . Set for Remote directory: .
    
    ![send-files-over-ssh!](Images/Phase3/send-files-over-ssh.jpg)

17. Build the "deploy-on-docker-using-ansible" job

    ![console-output!](Images/Phase3/console-output.jpg)

18. Check on the ansible-server if exists the .war file

        . su - ansadmin
        . pwd
        . ll

    ![check-on-ansible-server-war-file!](Images/Phase3/check-on-ansible-server-war-file.jpg)

    The webapp.war file exists on the /home/ansadmin directory

19. Create on the ansible-server a Dockerfile. The content will be the same of the Dockerfile we create on the docker-server:

        # Pull tomcat image
        FROM tomcat:8.0

        # Copy webapp.war to tomcat
        COPY ./webapp.war /usr/local//tomcat/webapps
    ![create-Dockerfile-on-ansible-server!](Images/Phase3/create-Dockerfile-on-ansible-server.jpg)

    From this Dockerfile, we will create an docker image and from the docker image, we will create a container using ansible playbook.
    So we are going to write an ansible playbook file.

20. Create an ansible playbook file

        ---
        - hosts: all
        become: true (become a root user)
        
        tasks:
        - name: building docker image using war file
          command: docker build -t tomcat-server-v8 -f Dockerfile .
          args:
            chdir: /home/ansadmin

        - name: building docker container of the image
          command: docker run -d --name tomcat-server-ctr -p 8080:8080 tomcat-server-v8
             
    ![create-ansible-docker-image-and-container-file!](Images/Phase3/create-ansible-docker-image-and-container-file.jpg)

    ![files-list-on-home-ansadmin!](Images/Phase3/files-list-on-home-ansadmin.jpg)

21. Check if we have docker images and containers on the ansible-server

    ![check-docker-images!](Images/Phase3/check-docker-images.jpg)
    
22. We need to create an image using the ansible playbook that will use the Dockerfile file.
    So, we have to create the "hosts" file on the /home/ansadmin directory with the content:

        . localhost
    ![create-hosts-file-on-home-ansadmin!](Images/Phase3/create-hosts-file-on-home-ansadmin.jpg)

    Because we will create an image and container om the local machine, not on the docker-server machine

23. Run the "ansible-playbook check" command to check if we have getting errors:

    . ansible-playbook -i hosts ansible-docker-image.yml --check

    ![check-ansible-playbook-check-command!](Images/Phase3/check-ansible-playbook-check-command.jpg)

    We can see that "failed=0", so we don't have any problem to run the ansible-playbook command

24. Run the ""ansible-playbook" command without the check attribute

        . ansible-playbook -i hosts ansible-docker-image.yml

    ![run-ansible-playbook-command!](Images/Phase3/run-ansible-playbook-command.jpg)

25. Check if the image and the container was created

        . docker images
        . docker ps

    ![check-create-image-and-container!](Images/Phase3/check-create-image-and-container.jpg)

26. Login in to the Jenkins-server and modify the "deploy-on-docker-using-ansible" job

        . Build Triggers: checked Poll SCM(The job will executed automatically)
        . Schedule: * * * * *(To execute the job each minute)
    ![build-triggers!](Images/Phase3/build-triggers.jpg)

    Post Steps
        
        . Remote directory: .
        . Exec command:
        ansible-playbook -i /home/ansadmin/hosts ansible-docker-image.yml;
    ![post-steps!](Images/Phase3/post-steps.jpg)

27. Change the index.jsp file

        . Add the line: <h1 style="color: blue;"> Version: 1.0 </h1>
    ![change-index-jsp!](Images/Phase3/change-index-jsp.jpg)

28. Run the "deploy-on-docker-using-ansible" job
    
    ![run-deploy-on-docker-using-ansible-job!](Images/Phase3/run-deploy-on-docker-using-ansible-job.jpg)

29. Browser on the ansible-server

    ![browse-on-the-ansible-server!](Images/Phase3/browse-on-the-ansible-server.jpg)