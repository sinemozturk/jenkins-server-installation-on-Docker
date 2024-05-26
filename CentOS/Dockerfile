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
