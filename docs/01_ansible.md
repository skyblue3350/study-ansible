# ansible について

- エージェントレス
- OSやソフトウェアなどの設定作業を自動化
- 処理内容を yaml で記述
- 冪等性あり

## 環境について

ansible は ssh を利用して対象 ノード の操作を行うため、何らかの方法で ssh 接続可能になっている必要がある。
以下のようなオプションを都度指定するか、ssh config や ansible.cfg を設定しておくと楽。

- `--user`
  ssh 接続を行う際に利用するユーザ名
- `--private-key`
  ssh 接続を行う際に利用する秘密鍵
- `--ask-pass`
  ssh 接続を行う際に利用するパスワード

## ディレクトリ構成について

基本的にベストプラクティスに従っておくのが良い。
その他の項目についても参考になるので一読推奨。

- [ベストプラクティス ディレクトリーのレイアウト — Ansible Documentation](https://docs.ansible.com/ansible/2.9_ja/user_guide/playbooks_best_practices.html#directory-layout)

基本的に機能単位ごとに role を分けて用意し、ディレクトリルートにある site.yaml 等で必要な role を呼び出す形が便利。

- site.yaml
- roles
  - ロール名
    - tasks
      - main.yaml
        メインとなる処理を記述する
    - defaults/vars
      - main.yaml
        変数の初期値を用意する
    - templates
      - hoge.j2
        中身を動的に変えたいファイルを jinja2 形式で記述し配置する
    - files
      - hoge
        静的なファイルを配置する

## インベントリについて

ansible で操作する対象をインベントリに登録して管理する。
以下のように OS や機能ごとにまとめておくことで実行対象をフィルタすることが可能となる。

```yaml
all:
  children:
    ubuntu:
      hosts:
        ubuntu01:
        ubuntu02:
    centos:
      hosts:
        centos01:
        centos02:
```

## サンプルロールについて

サンプルロール内で利用しているモジュールについては下記を参照。

- [Ansible.Builtin — Ansible Documentation](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/index.html)

また、ロールを作成する際に不足している機能はモジュールやプラグインを自作することで解決できる。開発方法は下記を参照。

- [Developing Ansible modules — Ansible Documentation](https://docs.ansible.com/ansible/latest/dev_guide/developing_modules_general.html)
- [Developing plugins — Ansible Documentation](https://docs.ansible.com/ansible/latest/dev_guide/developing_plugins.html)

### supervisor

docker コンテナ内では systemd が使えないため代用で用意している。
OS に応じてパッケージのインストールを行っている。

### nginx

nginx のインストール及び supervisor を利用したサービスの起動と正しくポートを listen しているかの確認を行っている。

## コマンドについて

上記の yaml ファイルの実行について記載する。

### 特定の対象にのみ実行

`-l SUBSET, --limit SUBSET` オプションを利用することで対象を制約することができる。
hosts ファイルで作成したグループ名やホスト名等を指定可能。

```
docker-compose exec ansible ansible-playbook site.yaml -l ubuntu01
PLAY RECAP *********
ubuntu01                   : ok=7    changed=1    unreachable=0    failed=0    skipped=3    rescued=0    ignored=0
docker-compose exec ansible ansible-playbook site.yaml -l ubuntu
PLAY RECAP *********
ubuntu01                   : ok=7    changed=1    unreachable=0    failed=0    skipped=3    rescued=0    ignored=0
ubuntu02                   : ok=7    changed=1    unreachable=0    failed=0    skipped=3    rescued=0    ignored=0
```

### 差分の表示

`-D, --diff` オプションを利用することで変更があった際に差分を確認することができる。

```
docker-compose exec ansible ansible-playbook site.yaml --diff
```

### dry-run

`-C, --check` オプションを利用することで変更は適用せず dry-run することができる。
`-D, --diff` オプションと併用することで変更点を事前に確認することができる。

```
docker-compose exec ansible ansible-playbook site.yaml --diff --check
```
