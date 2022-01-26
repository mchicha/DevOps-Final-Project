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
    - service docker start
    - service docker status

    ![service-docker-start!](Images/Phase3/service-docker-start.jpg)

5. Connect to the ansible-server via Putty
    
   Change hostname of the ec2 machine to ansible-server:
    - sudo su -
    - hostname ansible-server
    - sudo su -

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

    k. Log in as a ansadmin user on master and generate ssh key (on Control node)

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
        