FROM python

WORKDIR /workdir

RUN pip install ansible
RUN apt-get update && apt-get install -y sshpass curl

CMD ["sleep", "infinity"]
