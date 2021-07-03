# setup

## コンテナ構成

複数台での検証を行う場合は `docker-compose.yaml` で ubuntu02 のような形で増やす

- ansible
  - ansible 実行用のコンテナ
- ubuntu01
  - ansible で検証環境として利用するコンテナ
- centos01
  - ansible で検証環境として利用するコンテナ

## 環境構築

```
docker-compose up -d --build
docker-compose ps
       Name               Command        State   Ports
-------------------------------------------------------
ansible_ansible_1    sleep infinity      Up
ansible_centos01_1   /sbin/sshd -D       Up      22/tcp
ansible_ubuntu01_1   /usr/sbin/sshd -D   Up      22/tcp
```

## 接続先の疎通確認

```
docker-compose exec ansible ansible all -m ping
SSH password: hoge
ubuntu01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
centos01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/libexec/platform-python"
    },
    "changed": false,
    "ping": "pong"
}

docker-compose exec ansible ansible ansible all -m setup
centos01 | SUCCESS => {
        "ansible_architecture": "x86_64",
        "ansible_bios_date": "NA",
        "ansible_bios_vendor": "NA",
        ...
```

## 環境の初期化

コンテナの変更は restart することで破棄されるため、クリーンな環境にしたい場合は restart します。

```
docker-compose restart
```
