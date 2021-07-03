FROM centos

EXPOSE 22

RUN yum install -y openssh-server
RUN ssh-keygen -t rsa -N "" -f /etc/ssh/ssh_host_rsa_key
RUN echo 'root:hoge' | chpasswd


CMD ["/sbin/sshd", "-D"]
