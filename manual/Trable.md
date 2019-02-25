# トラブルシューティング
## レプリケーション
### show slave statusすると、error connecting to master ~~~が表示される
#### 1. 接続先のパスワードプラグインがcache_sha2_passwordである
- MySQL8.0では、パスワードプラグインが`cache_sha2_password`になっており、このままでは接続できないので、`mysql_native_password`に変更する
- 以下のコマンドで修正する

    ```sql
    mysql> alter user 'user-name'@'ip etc...' identified with 'mysql_native_password' by 'pass';
    ```

#### 2. 接続対象のホストのIPアドレスを間違えている
- 実際に何も考えずに以下のように設定し、接続できなかった。

    ```sql
    mysql> create user 'slave'@'192.168.56.11/255.255.255.0' identified by 'pass';
    ```

- ホストはIPアドレスのみでサブネットはいらない