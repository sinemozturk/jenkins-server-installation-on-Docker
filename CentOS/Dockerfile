FROM centos

# Install OpenSSH server
RUN yum -y install openssh-server

# Create a user and set up SSH access
RUN useradd remote_user && \
    echo "remote_user:1234" | chpasswd && \
    mkdir /home/remote_user/.ssh && \
    chmod 700 /home/remote_user/.ssh
    
# - useradd remote_user: creates the remote_user
# - echo "remote_user:1234" | chpasswd && \ is used to set the password for the remote_user
# - mkdir /home/remote_user/.ssh: creates the .ssh directory for SSH keys
# - chmod 700 /home/remote_user/.ssh: sets the directory permissions to 700

COPY remote-key.pub /home/remote-user/.ssh/authorized_keys

#The command COPY remote-key.pub /home/remote-user/.ssh is intended to
#     copy the remote-key.pub file from your local machine to
#         the /home/remote-user/.ssh/authorized directory inside the Docker image.



RUN chown remote_user:remote_user -R /home/remote-user/.ssh/ && \
    chmod 660 /home/remote_user/.ssh/authorized_keys

# The RUN chown remote_user:remote_user -R /home/remote-user/.ssh command in a Dockerfile
#   changes the ownership of the /home/remote-user/.ssh directory and all of its contents to 
#     the user remote_user. This is important for ensuring that the remote_user 
#      has the correct permissions to read and write files in the .ssh directory.
# The command you provided sets > chmod 660 /home/remote_user/.ssh/authorized_keys < 
#    the permissions of the /home/remote_user/.ssh/authorized_keys file to 660.

RUN /usr/sbin/sshd-keygen

# The command RUN /usr/sbin/sshd-keygen is typically used in 
#   a Dockerfile to generate SSH host keys for the SSH daemon

CMD /usr/sbin/sshd -D

# The CMD /usr/sbin/sshd -D instruction in 
# a Dockerfile is used to specify the command that should be run when a container start