# DevOps Project Phase 4 - Ansible Deploy to DockerHub 

  ![Project-Phase4!](Images/Phase4/project-phase-4.jpg)

1. Connect to the ansible-server via Putty

        . su - ansadmin
        . ls
    ![connect-to-ansible-server!](Images/Phase4/connect-to-ansible-server.jpg)

2. View the content of the ansible-docker-image.yml

        ---
        - hosts: all
        become: true

        tasks:
        - name: Stop container
            command: docker stop name tomcat-server-ctr
            ignore_errors: yes

        - name: Remove container
            command: docker rm tomcat-server-ctr
            ignore_errors: yes

        - name: Remove docker image
            command: docker rmi tomcat-server-v8
            ignore_errors: yes

        - name: building docker image using war file
            command: docker build -t tomcat-server-v8 -f Dockerfile .
            args:
            chdir: /home/ansadmin

        - name: building docker container out of image
            command: docker run -d --name tomcat-server-ctr -p 8080:8080 tomcat-server-v

3. Check if the service docker running

        . service docker status
    ![check-service-docker-status!](Images/Phase4/check-service-docker-status.jpg)

4. Check if existing docker containers and docker images

    . docker ps
    . docker images

    If it exists docker containers and docker images, so remove them.
    ![check-containers-and-images-existed!](Images/Phase4/check-containers-and-images-existed.jpg)

5. Run the ansible-docker-image.yml file with ansible-playbook locally(localhost)

        . ansible-playbook -i hosts ansible-docker-image.yml
    ![run-ansible-playbook-part1!](Images/Phase4/run-ansible-playbook-part1.jpg)
    ![run-ansible-playbook-part2!](Images/Phase4/run-ansible-playbook-part2.jpg)

6. Check if existing docker containers and docker images

        . docker ps
        . docker images
    ![check-containers-and-images-existed-again!](Images/Phase4/check-containers-and-images-existed-again.jpg)

7. Now we want to push the image to the docker hub account and we need to tag our image with the prefix of our account
    Tagging an image is very important otherwise the docker hub will not understand from which particaular account, the particular image has to move.
    So we will now tag the image
        . docker tag tomcat-server-v8 mordehaic/tomcat-server-v8
        . docker images
    ![docker-tag!](Images/Phase4/docker-tag.jpg)

    Now we have another image and this a the base image on which we have a customized image

8. Now, i will push this base image to the docker hub
        
        . docker push mordehaic/tomcat-server-v8
    ![docker-push-image-denied!](Images/Phase4/docker-push-image-denied.jpg)

        we got the next message: requested access to the resource is denied
        We have to login to the docker hub

9. Login to docker hub
        
        . docker login
    ![docker-login!](Images/Phase4/docker-login.jpg)

10. Push a new time the base image to the docker hub
        
        . docker push mordehaic/tomcat-server-v8
    ![docker-push-image-succeeded!](Images/Phase4/docker-push-image-succeeded.jpg)

        The push image succeeded
11. Check on the docker hub that the image existed
    ![browse-docker-hub!](Images/Phase4/browse-docker-hub.jpg)

12. Command to push a new tag to this repository with the image we push on the docker hub
        . docker push mordehaic/tomcat-server-v8:tagname
    ![push-a-new-tag-to-repository!](Images/Phase4/push-a-new-tag-to-repository.jpg)

13. Connect to the docker-server
    
    Run the service docker

        . service docker start
        . service docker status
    ![service-docker-start!](Images/Phase4/service-docker-start.jpg)

14. Check on the docker-server if existing images and containers

        . docker images
        . docker ps
        . docker ps -a
    ![check-if-existing-image-and-containers-on-docker-server!](Images/Phase4/check-if-existing-image-and-containers-on-docker-server.jpg)

15. On the ansible-server, remove also the mordehaic/tag image if existing

        . docker rmi mordehaic/tomcat-server-v8
    ![remove-tag-image!](Images/Phase4/remove-tag-image.jpg)

16. Pull the tag image from the docker hub on the ansible-server

        . docker pull mordehaic/tomcat-server-v8
    ![pull-tag-image-from-docker-hub!](Images/Phase4/pull-tag-image-from-docker-hub.jpg)

17. On the docker-server, switch to the ansadmin user because we will do all with this user

        . su - ansadmin
    ![su-ansadmin-on-docker-server!](Images/Phase4/su-ansadmin-on-docker-server.jpg)

18. Let 's check if the id belong to the docker group

        . id
        . exit
        . usermod -aG docker ansadmin(add the user to the docker group)
        . id
    ![check-id-belong-docker-group!](Images/Phase4/check-id-belong-docker-group.jpg)

19. Pull the mordehaic/tomcat-server-v8 image on the docker-server
        
        . docker pull mordehaic/tomcat-server-v8
        . docker images
    ![pull-image-on-docker-server!](Images/Phase4/pull-image-on-docker-server.jpg)

    So we succeeded to push the image to the docker hub and pull it to the both server: ansible-aerver and docker-server

20. Create a new ansible playbook: "ansible-dockerhub-image.yml" to create an image and push onto the docker hub
    
        ---
        - hosts: all
        become: true

        tasks:
        - name: Creating docker image
            command: docker build -t tomcat-server-v8 -f Dockerfile .
            args:
            chdir: /home/ansadmin

        - name: Adding tag to image
            command: docker tag tomcat-server-v8 mordehaic/tomcat-server-v8

        - name: Push image to dockerhub
            command: docker push mordehaic/tomcat-server-v8

        - name: Remove images from local
            command: docker rmi tomcat-server-v8 mordehaic/tomcat-server-v8
            ignore_errors: yes
    ![create-ansible-dockerhub-image!](Images/Phase4/create-ansible-dockerhub-image.jpg)

21. Login to docker on the ansible-server

        . docker login
    ![docker-login-on-ansible-server!](Images/Phase4/docker-login-on-ansible-server.jpg)

22. Check if existing docker containers. If existing so remove them.

        . docker ps
        . docker stop tomcat-server-ctr
        . docker rm tomcat-server-ctr
        . docker ps
        . docker ps -a
    ![docker-stop-container!](Images/Phase4/docker-stop-container.jpg)

23. Check if existing docker images. If existing so remove them

        . docker images
        . docker rmi tomcat-server-v8
        . docker rmi mordehaic/tomcat-server-v8
        . docker images
    ![docker-remove-images!](Images/Phase4/docker-remove-images.jpg)

24. Run with ansible-playbook the "ansible-dockerhub-image.yml" file

        . ansible-playbook -i hosts ansible-dockerhub-image.yml
    ![run-with-ansible-palybook-ansible-dockerhub-image!](Images/Phase4/run-with-ansible-palybook-ansible-dockerhub-image.jpg)

    The image was pushed to the docker hub

    ![image-pushed-to-dockerhub!](Images/Phase4/image-pushed-to-dockerhub.jpg)

25. We will create a new file that will be run with ansible-playbook to the deployment of the conatiner: "ansible-container-deployment.yml"

        ---
        - hosts: all
        #become: true

        tasks:
        - name: Stop container
            command: docker stop tomcat-server-ctr
            ignore_errors: yes

        - name: Remove container
            command: docker rm tomcat-server-ctr
            ignore_errors: yes

        - name: Remove docker image
            command: docker rmi mordehaic/tomcat-server-v8
            ignore_errors: yes

        - name: Pulling image from dockerhub
            command: docker pull mordehaic/tomcat-server-v8:latest

        - name: Building docker container from from the docker image pulled from dockerhub
            command: docker run -d --name tomcat-server-ctr -p 8080:8080 mordehaic/tomcat-server-v8

26. Run with ansible-playbook the "ansible-container-deployment.yml" file

        . ansible-playbook -i hosts ansible-container-deployment.yml
    ![run-with-ansible-palybook-ansible-container-deployment!](Images/Phase4/run-with-ansible-palybook-ansible-container-deployment.jpg)

27. Check if the container was created

        . docker ps
        . docker images
    ![check-container-created-with-ansible!](Images/Phase4/check-container-created-with-ansible.jpg)

    We can see that the container was created and the image was pulled from docker hub

28. We have to add the private ip of the docker-server on the "hosts" file to create the container on the docker-server

        The private IP of the docker-server is: 172.31.7.60
    ![add-docker-server-private-ip-to-hosts!](Images/Phase4/add-docker-server-private-ip-to-hosts.jpg)

29. After we add the docker-server private IP in the hosts file,
    Running with ansible-playbook the "ansible-dockerhub-image.yml" file, will pull the image from the docker hub to the docker-server

        . ansible-playbook -i hosts ansible-dockerhub-image.yml --limit 172.31.7.60
    ![run-ansible-palybook-ansible-dockerhub-image-to-docker-server!](Images/Phase4/run-ansible-palybook-ansible-dockerhub-image-to-docker-server.jpg)

30. Running with ansible-playbook the "ansible-container-deployment.yml" file, to create the    container on the docker-server

        . ansible-playbook -i hosts ansible-container-deployment.yml --limit 172.31.7.60
    ![run-ansible-palybook-ansible-container-deployment-to-docker-server!](Images/Phase4/run-ansible-palybook-ansible-container-deployment-to-docker-server.jpg)

31. Let's check, if the image and the container are present on the docker-server

        . docker images
        . docker ps
    ![image-and-container-present-on-docker-server!](Images/Phase4/image-and-container-present-on-docker-server.jpg)

32. Browser on the docker-server
    ![browse-on-docker-server!](Images/Phase4/browse-on-docker-server.jpg)

33. Creation a new job on Jenkins to do all the things automatically:  deploy-container-on-docker-server-using-ansible
    ### Part: Source code Management
        . Repository URL: https://github.com/mchicha/DevOps-Final-Project.git
        . Branch Specifier (blank for 'any'): */phase-4-ansible-deploy-to-dockerhub
    ![source-code-management!](Images/Phase4/source-code-management.jpg)

    ### Build Triggers
        . Build Triggers: checked Poll SCM(The job will executed automatically)
        . Schedule: * * * * *(To execute the job each minute)
     ![build-trigger!](Images/Phase4/build-trigger.jpg)
    
    ### Part: Build
        . Root POM: pom.xml
        . Goals and options: clean install package

    ![pre-steps!](Images/Phase4/pre-steps.jpg)

    ### Post build step
    
        . Click on Add post build step
        . Select: Send files or execute commands over SSH
        . Set for Name: ansible-server
        . Set for Source files: webapp/target/*.war
        . Set for Remove prefix: webapp/target
        . Set for Remote directory: .
        . Exec command: 
        ansible-playbook -i /home/ansadmin/hosts ansible-dockerhub-image.yml --limit localhost;
        ansible-playbook -i /home/ansadmin/hosts ansible-container-deployment.yml --limit 172.31.7.60
    ![post-steps!](Images/Phase4/post-steps.jpg)



