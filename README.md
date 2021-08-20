# mysql-replication
replication sandbox of mysql
```bash
.
├── Dockerfile
├── docker-compose.yml
├── master
│   └── conf
│       └── my.cnf
├── setup.sh
└── slave
    └── conf
        └── my.cnf
```

###
docker-compose up -d --remove-orphans

# バイナリログの有効化とサーバIDの付与
docker container exec -it mysql_master /usr/local/bin/setup.sh
docker container exec -it mysql_slave /usr/local/bin/setup.sh

# 再起動
docker-compose down
docker-compose up -d --remove-orphans


###　レプリケーションに関する主な設定
1. master側でレプリケーション用ユーザーの作成
CREATE USER 'repl'@'${IPアドレス}' IDENTIFIED BY '${パスワード}';
GRANT REPLICATION SLAVE ON *.* TO 'repl'@'${IPアドレス}';
2. master側のバイナリログの情報を確認
SHOW MASTER STATUS;
3. master側で取得したバイナリログの情報を元にslaveの設定
CHANGE MASTER TO
  MASTER_HOST='${master側のホスト名}',
  MASTER_PORT=3306,
  MASTER_LOG_FILE='${masterのbinlogのファイル名}',
  MASTER_LOG_POS=${binlogのポジション};
4. slaveの開始と確認
START SLAVE USER = 'repl' PASSWORD = '${パスワード}';
SHOW SLAVE STATUS;


### Reference
[Dockerで学ぶMySQLのレプリケーション(master/slave構成)](https://qiita.com/wf-yamaday/items/47434b8312737da25521)

[MySQLでレプリケーション設定](https://qiita.com/zaburo/items/ee9d4f5973e6e226b616)