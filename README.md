# WordPress Development Environment

Nginx + WordPress + MariaDB を使用した Docker ベースの WordPress 開発環境です。VS Code の Dev Container 機能を使用して、簡単に環境構築できます。

## 📋 必要な環境

- Docker Desktop
- Visual Studio Code
- VS Code 拡張機能: Dev Containers

## 🚀 セットアップ手順

### 1. リポジトリのクローン

```bash
git clone https://github.com/Shiyo1101/learn-wordpress.git
cd learn-wordpress
```

### 2. VS Code で Dev Container を起動

1. VS Code でプロジェクトフォルダを開く
2. コマンドパレットを開く（`Ctrl+Shift+P` / `Cmd+Shift+P`）
3. 「Dev Containers: Reopen in Container」を選択
4. 初回起動時はイメージのダウンロードとビルドに数分かかります

### 3. WordPress にアクセス

ブラウザで以下の URL を開きます：

```bash
http://localhost:8080
```

WordPress のインストール画面が表示されます。

## 🔧 初期設定

### WordPress のインストール

1. ブラウザで `http://localhost:8080` にアクセス
2. 言語を選択
3. サイトの情報を入力：
   - サイトのタイトル
   - ユーザー名
   - パスワード
   - メールアドレス
4. 「WordPress をインストール」をクリック

### データベース接続情報

WordPress は自動的にデータベースに接続されますが、手動で設定する場合は以下の情報を使用します：

- **データベース名**: `wordpress_db`
- **ユーザー名**: `wordpress`
- **パスワード**: `wordpress_password`
- **データベースホスト**: `mariadb:3306`

## 📁 プロジェクト構成

```bash
project/
├── .devcontainer/
│   └── devcontainer.json          # Dev Container設定
├── nginx/
│   └── nginx.conf                 # Nginx設定ファイル
├── docker-compose.yml             # Docker Compose設定
├── .gitignore                     # Git管理除外ファイル
└── README.md                      # このファイル
```

## 🛠 開発ワークフロー

### テーマ・プラグインの開発

Dev Container 内では、`/var/www/html`が WordPress のルートディレクトリです。

```bash
# テーマの作成
cd /var/www/html/wp-content/themes
mkdir my-custom-theme

# プラグインの作成
cd /var/www/html/wp-content/plugins
mkdir my-custom-plugin
```

### コンテナの操作

```bash
# コンテナの状態確認
docker-compose ps

# コンテナの再起動
docker-compose restart

# コンテナの停止
docker-compose down

# コンテナの停止＆データ削除（注意！）
docker-compose down -v
```

### ログの確認

```bash
# すべてのコンテナのログ
docker-compose logs

# 特定のコンテナのログ
docker-compose logs nginx
docker-compose logs wordpress
docker-compose logs mariadb

# リアルタイムでログを表示
docker-compose logs -f
```

## 🔒 セキュリティ設定

### 本番環境用の設定変更

本番環境にデプロイする前に、以下の設定を必ず変更してください：

1. **データベースパスワードの変更**

   - `docker-compose.yml`内の以下の値を変更：
     - `MYSQL_ROOT_PASSWORD`
     - `MYSQL_PASSWORD`
     - `WORDPRESS_DB_PASSWORD`

2. **WordPress 認証キーの設定**
   - `wp-config.php`で認証キーを設定
   - <https://api.wordpress.org/secret-key/1.1/salt/> から取得

## 📊 データベース管理

### データベースのバックアップ

```bash
docker-compose exec mariadb mysqldump -u wordpress -pwordpress_password wordpress_db > backup.sql
```

### データベースのリストア

```bash
docker-compose exec -T mariadb mysql -u wordpress -pwordpress_password wordpress_db < backup.sql
```

### データベースへの直接アクセス

```bash
docker-compose exec mariadb mysql -u wordpress -pwordpress_password wordpress_db
```

## 🐛 トラブルシューティング

### ポート 8080 が既に使用されている

`docker-compose.yml`のポート番号を変更します：

```yaml
ports:
  - "8081:80" # 8080を8081に変更
```

### ファイルの権限エラー

コンテナ内で権限を修正します：

```bash
docker-compose exec wordpress chown -R www-data:www-data /var/www/html
```

### データベース接続エラー

1. MariaDB コンテナが起動しているか確認：

   ```bash
   docker-compose ps
   ```

2. MariaDB のログを確認：

   ```bash
   docker-compose logs mariadb
   ```

3. コンテナを再起動：

   ```bash
   docker-compose restart mariadb wordpress
   ```

### キャッシュのクリア

```bash
# Dockerイメージを再ビルド
docker-compose build --no-cache

# コンテナを再作成
docker-compose up -d --force-recreate
```

## 🔄 本番環境へのデプロイ

1. カスタムテーマ・プラグインをエクスポート
2. データベースをエクスポート
3. `wp-content/uploads/`ディレクトリをエクスポート
4. 本番サーバーで新しい環境を構築
5. ファイルとデータベースをインポート
6. `wp-config.php`のデータベース接続情報を更新

## 📚 参考リンク

- [WordPress Codex](https://wpdocs.osdn.jp/)
- [Docker Documentation](https://docs.docker.com/)
- [Nginx Documentation](https://nginx.org/en/docs/)
- [MariaDB Documentation](https://mariadb.org/documentation/)

## 📝 ライセンス

このプロジェクトは開発環境の設定ファイルです。WordPress は[GPLv2](https://wordpress.org/about/license/)ライセンスの下で配布されています。

## 🤝 コントリビューション

バグ報告や機能リクエストは、Issues で受け付けています。プルリクエストも歓迎します！
