# CREATE A JENKINS SERVER ON DOCKER CONTAINER

To install a Jenkins server on a Docker container, you can follow these steps:

## S T E P S 
1. Pull the Jenkins Docker image:
- Open your terminal and run the following command to pull the official Jenkins image from Docker Hub:

```bash
docker pull jenkins/jenkins:lts
```

2. Run the Jenkins container:
- Once the image is downloaded, you can create and run a Jenkins container using the following command:

```bash
docker run -d -p 8080:8080 -p 50000:50000 --name jenkins -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts
```
- **Commnand Breakdown**:
 - `-d` runs the container in detached mode,
 - `-p` `8080:8080` maps port `8080` on your *host* to *port* `8080` on the container,
 - `-p` `50000:50000` maps port `50000` on your *host* to *port* `50000` on the container.
- The `-v jenkins_home:/var/jenkins_home` option **mounts** a *volume* for `Jenkins data persistence.`


3. Access Jenkins:
- Open your *web browser* and navigate to `http://localhost:8080`. You should see the Jenkins initial setup screen


![](./images/j1.jpeg)

4. Unlock Jenkins:
- The initial setup requires a password stored in the Jenkins home directory inside the container. You can get this password by running:

```bash
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```
![](./images/D1.jpg)


- **Commnand Breakdown**:

- `docker exec:` The docker exec command is used to run a command in a running Docker container. This command allows you to interact with the container's file system and processes.
- `jenkins:` This is the name of the running Docker container in which you want to execute the command. In this case, the container is named jenkins.

- `cat /var/jenkins_home/secrets/initialAdminPassword`:
    - `cat:`The cat command is used to read and display the content of files.
    - `/var/jenkins_home/secrets/initialAdminPassword`:This is the file path inside the Jenkins container where the initial admin password is stored. Jenkins creates this file during the initial setup process.

5. Install suggested plugins:
- Follow the on-screen instructions to install the suggested plugins or customize the installation based on your needs.

6. Create an admin user:
- Once the plugins are installed, you'll be prompted to create an admin user. Complete the setup and start using Jenkins.

![](./images/j2.jpg)


- Welcome to Jenkins

![](./images/j3.jpg)

## SSH INTO JENKINS SERVER RUNNING ON DOCKER CONTAINER


To SSH into a Jenkins container (or any Docker container), you can use the docker exec command with an interactive shell. Here’s how you can do it:


- Check if the Jenkins container is running:

```bash
docker ps
```

![](./images/d2.jpg)

- SSH into the Jenkins container:

```bash
docker exec -it jenkins bash
```

- You should now see:

![](./images/d3.jpg)


## HOW TO EXECUTE JOBS IN A REMOTE MACHINE (ANOTHER DOCKER CONTAINER) WITH JENKINS

First of all we will create a container and then we will configure it. 

- Create a folder and name `jenkins-data` and cd inside

```bash
mkdir jenkins-data
cd jenkins-data
```

- Create a folder and name it `CentOS`
```bash
mkdir CentOS
ls
```

- Create a [Dockerfile](/CentOS/Dockerfile) for CentOS image ; 

```bash
touch Dockerfile
```

- Now you can use text editor tools (nano,vim..) copy and paste following; 

```Dockerfile
FROM centos:7

# Install OpenSSH server
RUN yum -y install openssh-server

# Create a user and set up SSH access
RUN useradd remote_user && \
    echo "remote_user:1234" | chpasswd && \
    mkdir /home/remote_user/.ssh && \
    chmod 700 /home/remote_user/.ssh
```


## Create SSH Key for the remote connection

The command `ssh-keygen -f remote-key `is used to generate SSH keys and specifies the output file name for the key. Here's a detailed explanation of the command:

```bash
ssh-keygen -f remote-key
```
![](./images/d5.jpg)

- Now we need to copy this key into remote container 
- Add following file to [Dockerfile](/CentOS/Dockerfile)

```Dockerfile
COPY remote-key.pub /home/remote_user/.ssh/authorized_keys

```

- We need to make sure the remote-user that we created earlier is the owner of everything under the `/home/remote_user/.ssh` folder. Add following scripts to Dockerfile

```Dockerfile
RUN chown remote_user:remote_user -R /home/remote-user/.ssh/ && \
    chmod 660 /home/remote_user/.ssh/authorized_keys
```

- SSH needs to create some global keys, following command is create the global keys

```Dockerfile
RUN ssh-keygen -A
```

- Start the SSH server

```Dockerfile
CMD /usr/sbin/sshd -D
```

- Now we need to modify [docker-compose.yml](./Jenkins/docker-compose.yml) file.

```yml
version: '3'
services:
  jenkins:
    container_name: jenkins
    image: jenkins/jenkins:lts
    ports:
      - "8080:8080"
    volumes:
      - "$PWD/jenkins_home:/var/jenkins_home"
    networks:
      - net
  remote_host:
    container_name: remote-host
    image: remote-host
    build:
      context: CentOS
    networks:
      - net
networks:
  net:
```

```sh
sudo chown 1000:1000 jenkins_home -R
```


### Build Remote-Host Conrainer from Dockerfile

- We created our Dockerfile for Centos container. Now we need to build our custom image with following;

```bash
docker-compose build
```

![](./images/centos1.jpg)


- To check Docker image thay we just created with Dockerfile

```bash
docker images
```

![](./images/centos%20image.jpg)

- Now we need to create our Centos `container` from this custom image

```bash
docker-compose up -d
```
![](./images/centos%20container.jpg)

- We successfully created 2 continers, check containers; 

```bash
docker ps
```

![](./images/both%20containers.jpg)

### Connect to CentOS from Jenkins container

- First of all, we need to copy the `remote-key.pub` inside the Centos folder to jenkins /temp file

```bash
docker cp remote-key.pub <<containerid or name >>:/tmp/remote-key
```

![](./images/remote%20key%20copy.jpg)


- Now we need to connect Jenkins container

```bash
docker exec -it <<container id >> bash
```
![](./images/jenkins-ssh.jpg)

- You can cd to /tmp folder and do ls

```bash
cd /tmp
ls
```

![](./images/key%20in%20jenkins.jpg)

- Now let's connect to our Centos container from Jenkins container

```bash
ssh -i remote-key  remote_user@remote_host
```

![](./images/connection%20to%20centos.jpg)








```bash

```
```bash

```
```bash

```

```bash

```
```bash

```
```bash

```
```bash

```
```bash

```
```bash

```
```bash

```
```bash

```
