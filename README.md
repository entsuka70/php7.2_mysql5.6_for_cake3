参考リンク

- https://shnr.net/blog/?p=932
- https://qiita.com/ciloholic/items/c9bbb604d8338551b5dc
- https://tt-computing.com/docker-cake4-apache-mysql

手順

1. git clone このリポジトリ
2. docker-compose build (ルートディレクトリ)
   よくここでコケることが多い。unzip がない、intl がないでコケたこと有り
   必要に応じてコンテナ、イメージを削除しつつ Dockerfile を修正して再度ビルドをトライ
3. docker-compose up -d
   コンテナが起動すれば、DB, WEB アクセス可能
   URI は apache の設定でサブドメインで表示するようにしている
   WEB:https://app.localhost:8080
   DB:https://localhost:8888
4. phpmyadmin のホスト名を修正※なんとかしたい
   4-1. docker exec -it cake3appdocker_db_1 /bin/sh (cake3appdocker : アプリ？ディレクトリ名。コンテナから確認できる)
   4-2. mysql にログイン (mysql -u root -p; パスワード聞かれるので docker-compose.yml で設定した password を入力)
   4-3. select @@hostname; (出力した hostname をコピーして docker-compose.yml の PMA_HOST を書き換える変更する。)
   4-4. phpmyadmin のみ再ビルド
   4-4-1. docker-compose build phpmyadmin
   4-4-2. docker-compose up -d phpmyadmin
5. docker のコンテナに入る
   5-1. docker exec -it cake3appdocker_web_1 /bin/bash
   5-2. curl -sS https://getcomposer.org/installer | php (composer インストール)
   5-3. php composer.phar create-project --prefer-dist cakephp/app:3.5 (cakephp3.5 インストール)
   intl とか入っていないとコケる
6. DB 作成
   6-1. docker exec -it cake3appdocker_db_1 /bin/sh (DB コンテナに入る)
   6-2. mysql にログイン (mysql -u root -p;)
   6-3. CREATE DATABASE DB_NAME;
   6-4. インストールしてある cakephp アプリの DB 接続設定を変更
   6-5 app.php の Datasource の host, username, password を変更
   ※ host: db (docker-compose.yml の db が該当)
