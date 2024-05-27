# Explanation:
`version:` '3': Specifies the version of the Docker Compose file format.
`services:` Defines the services that make up your application.

- jenkins:

`image:` jenkins/jenkins:lts: Uses the latest long-term support (LTS) version of the Jenkins image.

`ports:` - "8080:8080": Maps port 8080 on your host to port 8080 in the Jenkins container.

`volumes:` - "$PWD/jenkins_home:/var/jenkins_home": Mounts a volume from the current directory ($PWD/jenkins_home) to the Jenkins home directory inside the container (/var/jenkins_home).

`networks:` - net: Connects the Jenkins container to the net network.

- remote_host:

`container_name:` remote-host: Names the container remote-host.

`image:` remote-host: Specifies the image name as remote-host. You will need to build this image.

`build: `context: ./CentOS: Builds the remote-host image from the Dockerfile located in the ./CentOS directory.

`networks:` - net: Connects the remote_host container to the net network.

`networks:` Defines the network named net which is used by both services.