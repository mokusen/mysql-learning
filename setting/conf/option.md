# オプション

## log_slave_updates
- `log_slave_updates`を設定することで、スレーブサーバーにもマスターから**送られてきた更新をバイナリログに書き込める**ようになる。
- スレーブサーバーとして使用しているDBサーバーをマスターとして扱い、更にスレーブサーバーを作成するできる
- 以下のような構成を行いたいときに設定する

![replication-log-slave-updates.PNG](../../image/replication-log-slave-updates.png)

