# 1. 事前準備
- マスターとスレーブサーバーを用意する

# 2. レプリケーション設定
## 2-1. マスターの設定
### 2-1-1. server_id、バイナリログ有効化を設定する
- `my.cnf`に一意の`server_id`、`log_bin`を記述する
- IPアドレスでいいかな

### 2-1-2. MySQLにスレーブ接続用のアカウントを作成する

```sql
mysql> CREATE USER 'mokusen'@'192.168.30.5' IDENTIFIED WITH 'mysql_native_password' BY 'secret';
mysql> GRANT REPLICATION SLAVE ON *.* TO 'mokusen'@'192.138.30.5';
```

### 2-1-3. スレーブへのコピーデータを作成する
- 新規でない場合を想定する
- マスターサーバーで以下を実行する

```bash
mysqldump -h localhost -u root -p -A --single-transaction --quick --master-data=2 > master.dump
```

### 2-1-4. 作成したdumpファイルから、MASTER_LOG_FILEとMASTER_LOG_POSを取得する
- 以下のコードを実行する

```bash
cat master.dump | grep "MASTER_LOG_FILE='"
```

- 返却値が以下であると仮定する

```bash
-- CHANGE MASTER TO MASTER_LOG_FILE='binlog.000001', MASTER_LOG_POS=100;
```

## 2-2. スレーブの設定
### 2-2-1. server_id、バイナリログ有効化を設定する
- `my.cnf`に一意の`server_id`、`log_bin`を記述する
- IPアドレスでいいかな

### 2-2-2. レプリケーションの設定
- スレーブサーバーで以下を実行する

```sql
mysql> CHANGE MASTER TO
    ->   MASTER_HOST='192.168.30.5',
    ->   MASTER_PORT=3306,
    ->   MASTER_LOG_FILE='binlog.000001',
    ->   MASTER_LOG_POS=100,
    ->   MASTER_HEARTBEAT_PERIOD=60;
```

### 2-2-3. レプリケーションの開始
- 以下を実行する

```sql
mysql> START SLAVE USER='mokusen' PASSWORD='secret';
```

## 3. レプリケーションをやめる
- 以下のコマンドを実行する

```sql
mysql> stop slave;
mysql> reset slave;
```

- `mysqld`を再起動する