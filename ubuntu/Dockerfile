FROM ubuntu

EXPOSE 22

RUN apt-get update && apt-get install -y openssh-server
RUN mkdir -p /run/sshd
RUN echo 'root:hoge' | chpasswd
RUN sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config

CMD ["/usr/sbin/sshd", "-D"]
