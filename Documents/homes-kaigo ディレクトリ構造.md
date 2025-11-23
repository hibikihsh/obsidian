---
category:
  - lifull-kaigo
---

## プロジェクト概要

LIFULL KAIGO Application のディレクトリ構造とファイル配置に関する完全な仕様書

## 初学者向けガイド

### このプロジェクトの全体像

このプロジェクトは、介護施設検索サービス「LIFULL介護」のWebアプリケーションです。
以下の3つの主要な部分で構成されています：

1. **フロントエンド（ユーザーが見る画面）**
   - PC版サイト：一般ユーザー向けの介護施設検索サイト
   - スマホ版サイト：スマートフォン最適化版
   - 管理画面：施設情報を管理する画面

2. **バックエンド（裏側の処理）**
   - PHP（Symfony）：サーバーサイドのビジネスロジック
   - データベース（PostgreSQL）：施設情報や相談データの保存
   - API：外部システムとの連携

3. **インフラ（開発環境）**
   - Docker：開発環境を簡単に構築するツール
   - Webpack：CSS/JavaScriptをビルドするツール

### ディレクトリの役割を理解する

**「触る」ディレクトリ（開発でよく使う）**
- `src/` - PHPのコードを書く場所（最も重要）
- `assets_webpack/` - CSS/JavaScriptのソースコードを書く場所
- `apps/` - アプリケーションの設定ファイル

**「見る」ディレクトリ（参照するだけ）**
- `htdocs/` - Webサーバーが公開するファイル（画像、フォントなど）
- `docs/` - データベース設計書やSQL
- `assets/` - ビルド済みのCSS/JS（自動生成）

**「触らない」ディレクトリ（自動生成）**
- `vendor/` - PHPライブラリ（composer installで自動生成）
- `node_modules/` - Node.jsライブラリ（yarn installで自動生成）
- `var/` - キャッシュやログ（アプリが自動生成）

### よくある作業とディレクトリの関係

| やりたいこと | 触るディレクトリ | 説明 |
|------------|----------------|------|
| 新しい機能を追加 | `src/` | Controllerやサービスクラスを作成 |
| デザインを変更 | `assets_webpack/styles/` | SCSSファイルを編集 |
| JavaScriptを追加 | `assets_webpack/scripts/` | TypeScript/JSファイルを編集 |
| データベースを変更 | `docs/postgresql/migrations/` | マイグレーションSQLを作成 |
| 設定を変更 | `apps/[アプリ名]/config/` | YAMLファイルを編集 |
| 画像を追加 | `htdocs/[アプリ名]/img/` | 画像ファイルを配置 |

## ルートディレクトリ構造

```
homes-kaigo/
├── .github/                      # GitHub設定
├── apps/                         # アプリケーション設定
├── assets/                       # 静的アセット（CSS/JS）
├── assets_webpack/               # Webpack用ソースファイル
├── bin/                          # 実行可能ファイル
├── docker/                       # Docker設定
├── docs/                         # ドキュメント・スクリプト
├── htdocs/                       # 公開ディレクトリ
├── src/                          # ソースコード
├── tests/                        # テストファイル
├── var/                          # 一時ファイル・キャッシュ
├── vendor/                       # Composer依存パッケージ
├── webpack/                      # Webpack設定
├── .env                          # 環境変数（Git管理外）
├── .env.example                  # 環境変数テンプレート
├── composer.json                 # PHP依存関係定義
├── composer.lock                 # PHP依存関係ロック
├── docker-compose.dev.yml        # Docker Compose設定
├── package.json                  # Node.js依存関係定義
├── yarn.lock                     # Yarn依存関係ロック
└── README.md                     # セットアップガイド
```

## 主要ディレクトリ詳細

### 1. apps/ - アプリケーション設定

**役割**: Symfonyアプリケーションの起動設定と環境別の設定を管理

**初学者向け説明**:
このプロジェクトは1つのシステムですが、用途別に7つのアプリケーションに分かれています。
例えば、一般ユーザー向けのPC版サイト、スマホ版サイト、施設管理者向けの管理画面などです。
それぞれのアプリケーションは独立して動作し、異なる設定を持つことができます。

```
apps/
├── _kaigo_common/                # 共通設定
│   └── config/                   # 環境別設定（dev/live/pool/test/backstage）
├── _symfony/                     # Symfony基本設定
│   ├── config/                   # Symfony設定ファイル
│   ├── AppCache.php
│   └── AppKernel.php
├── kaigo_admin/                  # 管理画面アプリ
├── kaigo_api/                    # APIアプリ
├── kaigo_console/                # コンソールアプリ
├── kaigo_manager/                # 施設管理画面アプリ
├── kaigo_pc_client_manager/      # PC版クライアント管理アプリ
├── kaigo_pc_front/               # PC版フロントアプリ
└── kaigo_sp_front/               # SP版フロントアプリ
```

各アプリケーションディレクトリには以下が含まれます：
- `config/` - アプリケーション固有の設定
- `AppKernel.php` - アプリケーションカーネル
- `console` - コンソールコマンド実行ファイル
- `phpunit.xml.dist` - PHPUnit設定

### 2. assets/ - 静的アセット

**役割**: Webpackでビルドされた、ブラウザで実行可能なCSS/JSファイル

**初学者向け説明**:
このディレクトリのファイルは**自動生成**されます。直接編集しないでください。
編集したい場合は、`assets_webpack/`のソースファイルを編集してから、Webpackでビルドします。

**ビルドの流れ**:
1. `assets_webpack/`でTypeScript/SCSSを編集
2. Webpackでビルド実行
3. `assets/`に最適化されたファイルが生成される

```
assets/
└── kaigo/
    ├── css/                      # コンパイル済みCSS
    │   ├── admin/                # 管理画面用
    │   ├── cl_manager/           # クライアント管理用
    │   ├── manager/              # 施設管理用
    │   ├── pc/                   # PC版用
    │   ├── sp/                   # SP版用
    │   └── vendor/               # サードパーティCSS
    └── js/                       # コンパイル済みJS
        ├── apps/
        ├── effects/
        ├── events/
        ├── lib/
        ├── page/
        ├── vendor/
        ├── vm/
        └── webpack/
```

### 3. assets_webpack/ - Webpack用ソース

**役割**: フロントエンドのソースコード（ビルド前）

**初学者向け説明**:
CSS/JavaScriptを書く場所です。ここでは最新の記法（TypeScript、SCSS）が使えます。

**ディレクトリの使い分け**:
- `scripts/` - TypeScript/JavaScriptのコード
  - `admin/` - 管理画面用のスクリプト
  - `pc/` - PC版サイト用
  - `sp/` - スマホ版サイト用
  - `_libs/` - 共通で使う関数やクラス
  - `_vendor/` - 外部ライブラリ（jQuery、Vue.jsなど）
- `styles/` - SCSS（CSSの拡張記法）
- `images/` - 画像の元ファイル

```
assets_webpack/
├── images/                       # 画像ソース
│   └── admin/
├── scripts/                      # TypeScript/JavaScriptソース
│   ├── _libs/                    # 共通ライブラリ
│   ├── _vendor/                  # サードパーティライブラリ
│   ├── _vue/                     # Vue.js関連
│   ├── admin/                    # 管理画面用スクリプト
│   ├── pc/                       # PC版用スクリプト
│   └── sp/                       # SP版用スクリプト
└── styles/                       # SCSSソース
    ├── admin/                    # 管理画面用スタイル
    ├── pc/                       # PC版用スタイル
    └── sp/                       # SP版用スタイル
```

### 4. docker/ - Docker設定

**役割**: 開発環境をコンテナで構築するための設定

**初学者向け説明**:
Dockerを使うと、全員が同じ開発環境で作業できます。
- `php/` - PHP 7.2の実行環境
- `node-js/` - Node.jsの実行環境（Webpack用）
- `settings/certs/` - 社内ネットワーク用の証明書

**使い方**:
```bash
# Docker起動
docker compose -f docker-compose.dev.yml up

# PHPコンテナに入る
docker exec -it homes-kaigo-php bash

# Node.jsコンテナに入る
docker exec -it homes-kaigo-node-js bash
```

```
docker/
├── node-js/
│   └── dev.dockerfile            # Node.js開発環境
├── php/
│   └── dev.dockerfile            # PHP開発環境
└── settings/
    └── certs/                    # 証明書格納
        ├── .its_dummy_but_dont_remove_it
        └── netskope-root-ca.crt  # Netskope証明書（手動配置）
```

### 5. docs/ - ドキュメント

**役割**: データベース設計やSQL、外部連携の設定

**初学者向け説明**:
開発に必要な技術ドキュメントが格納されています。
- `postgresql/` - データベース関連
  - `initial/` - データベースの初期構築SQL
  - `migrations/` - テーブル変更のSQL（マイグレーション）
- `elasticsearch/` - 検索エンジンの設定
- `salesforce/` - メール配信の設定

**よくある使い方**:
テーブル構造を変更したい場合、`migrations/`に新しいSQLファイルを作成します。

```
docs/
├── elasticsearch/                # Elasticsearch設定
│   ├── scripts/                  # Groovyスクリプト
│   └── templates/                # インデックステンプレート
├── postgresql/                   # PostgreSQL設定
│   ├── initial/                  # 初期化SQL
│   ├── migrations/               # マイグレーションSQL
│   └── update/                   # 更新SQL
└── salesforce/                   # Salesforce設定
    ├── html/                     # メールテンプレート（HTML）
    └── text/                     # メールテンプレート（テキスト）
```

### 6. htdocs/ - 公開ディレクトリ

**役割**: Webサーバーが直接公開するファイル（ドキュメントルート）

**初学者向け説明**:
ブラウザから直接アクセスできるファイルを置く場所です。
- `app.php` / `app_dev.php` - アプリケーションのエントリーポイント（最初に実行されるファイル）
- `img/` - 画像ファイル（ロゴ、アイコンなど）
- `pdf/` - PDFファイル（利用規約など）
- `static/` - ビルド済みのCSS/JS

**重要**:
PHPのソースコード（`src/`）は、セキュリティのためこのディレクトリの外に置かれています。

```
htdocs/
├── _symfony/                     # Symfony基本エントリーポイント
│   ├── app_dev.php               # 開発環境エントリーポイント
│   ├── app.php                   # 本番環境エントリーポイント
│   └── .htaccess
├── kaigo_admin/                  # 管理画面公開ディレクトリ
│   ├── img/                      # 画像ファイル
│   ├── static/                   # 静的ファイル（CSS/JS）
│   ├── svg/                      # SVGファイル
│   ├── app_dev.php
│   └── app.php
├── kaigo_pc/                     # PC版公開ディレクトリ
│   ├── font/                     # フォントファイル
│   ├── img/                      # 画像ファイル
│   ├── pdf/                      # PDFファイル
│   ├── pdfjs/                    # PDF.js
│   ├── svg/                      # SVGファイル
│   ├── app_dev.php
│   └── app.php
└── kaigo_sp/                     # SP版公開ディレクトリ
    ├── img/                      # 画像ファイル
    ├── svg/                      # SVGファイル
    ├── app_dev.php
    └── app.php
```

### 7. src/ - ソースコード

**役割**: PHPのビジネスロジック（最も重要なディレクトリ）

**初学者向け説明**:
アプリケーションの中核となるPHPコードがすべてここにあります。
Symfonyの「バンドル」という単位で機能が分かれています。

**3つの階層**:
1. **Kaigo/** - アプリケーション層（画面や処理の流れ）
   - `PcFrontBundle` - PC版サイトの画面
   - `SpFrontBundle` - スマホ版サイトの画面
   - `PcAdminBundle` - 管理画面
   - `ApiBundle` - API
   - `ConsoleBundle` - バッチ処理

2. **LifullKaigo/** - ドメイン層（ビジネスロジック）
   - `FacilityReviewBundle` - 施設レビュー機能
   - `TourReservationBundle` - 見学予約機能
   - `ConsultationBundle` - 相談機能
   - など、機能ごとに30個以上のバンドル

3. **Lifull/** - 基盤層（共通フレームワーク）
   - `FrameworkBundle` - LIFULL共通の機能

**バンドルの中身**:
各バンドルは以下の構造を持ちます：
- `Controller/` - リクエストを受け取り、レスポンスを返す
- `Entity/` - データベースのテーブルに対応するクラス
- `Repository/` - データベースへの問い合わせ
- `Services/` - ビジネスロジック
- `Resources/views/` - Twigテンプレート（HTML）

```
src/
├── Bundle/
│   └── NextBundle/               # Next共通バンドル
├── Kaigo/                        # 介護アプリケーションバンドル
│   ├── AjaxBundle/               # Ajax処理
│   ├── ApiBundle/                # API
│   ├── ConsoleBundle/            # コンソールコマンド
│   ├── DomainBundle/             # ドメインロジック
│   ├── FrontBundle/              # フロント共通
│   ├── ManagerBundle/            # 管理機能
│   ├── PcAdminBundle/            # PC管理画面
│   ├── PcClientManagerBundle/    # PCクライアント管理
│   ├── PcFrontBundle/            # PCフロント
│   ├── PcManagerBundle/          # PC施設管理
│   ├── RedirectFrontBundle/      # リダイレクト処理
│   └── SpFrontBundle/            # SPフロント
├── Lifull/
│   └── FrameworkBundle/          # LIFULLフレームワーク
└── LifullKaigo/                  # LIFULL介護機能バンドル
    ├── AddressBundle/            # 住所機能
    ├── AdsBundle/                # 広告機能
    ├── ArchiveFacilityBundle/    # 施設アーカイブ
    ├── AreaArticleBundle/        # エリア記事
    ├── AsukoeBundle/             # あすこえ機能
    ├── AuditLogBundle/           # 監査ログ
    ├── BigQueryBundle/           # BigQuery連携
    ├── BrandCompanyBundle/       # ブランド企業
    ├── ConsultationBundle/       # 相談機能
    ├── ContentTypeBundle/        # コンテンツタイプ
    ├── DigitalBrochureBundle/    # デジタルパンフレット
    ├── FacilityReviewBundle/     # 施設レビュー
    ├── FreephoneBundle/          # フリーダイヤル
    ├── ImageRegulationBundle/    # 画像規制
    ├── InsurancePresentBundle/   # 保険プレゼント
    ├── LandingAreaPageBundle/    # ランディングエリアページ
    ├── LongTermCacheBundle/      # 長期キャッシュ
    ├── ManagerAccountBundle/     # 管理アカウント
    ├── MasterDataBundle/         # マスターデータ
    ├── MoveInCaseBundle/         # 入居事例
    ├── ProductContractBundle/    # 商品契約
    ├── ProfilerBundle/           # プロファイラー
    ├── SalesforceBundle/         # Salesforce連携
    ├── SecureFrameBundle/        # セキュアフレーム
    ├── SeniorAuthBundle/         # シニア認証
    ├── SEOLinkBundle/            # SEOリンク
    ├── TargetAreaBundle/         # ターゲットエリア
    ├── TargetingRecommendBundle/ # ターゲティングレコメンド
    ├── TourReservationBundle/    # 見学予約
    └── TrafficBundle/            # トラフィック
```

各バンドルには以下が含まれます：
- `Controller/` - コントローラー
- `Entity/` - エンティティ
- `Repository/` - リポジトリ
- `Services/` - サービス
- `Resources/` - リソース（設定、ビュー）
- `DependencyInjection/` - DIコンテナ設定

### 8. vendor/ - Composer依存パッケージ

**役割**: PHPの外部ライブラリ（自動生成、編集禁止）

**初学者向け説明**:
このディレクトリは**絶対に直接編集しないでください**。
`composer install`コマンドで自動生成されます。

**ライブラリの追加方法**:
1. `composer.json`に追加したいライブラリを記述
2. `composer update`を実行
3. `vendor/`に自動的にダウンロードされる

主要なパッケージ：
- `symfony/` - Symfonyフレームワーク
- `doctrine/` - Doctrine ORM
- `google/` - Google API
- `aws/` - AWS SDK
- `twig/` - Twigテンプレートエンジン
- `monolog/` - ログライブラリ
- `guzzlehttp/` - HTTPクライアント

### 9. webpack/ - Webpack設定

**役割**: フロントエンドのビルド設定

**初学者向け説明**:
TypeScript/SCSSをブラウザで動くJavaScript/CSSに変換する設定です。
通常は触る必要はありませんが、ビルドの挙動を変えたい場合に編集します。

**設定ファイルの種類**:
- `admin/` - 管理画面用のビルド設定
- `front/` - フロント用のビルド設定
- `_common/` - 共通設定

```
webpack/
├── _common/                      # 共通設定
│   ├── _settings.js
│   ├── _unused_sprite.js
│   └── utils.js
├── admin/                        # 管理画面用設定
│   ├── css.config.js
│   ├── img.config.js
│   └── js.config.js
└── front/                        # フロント用設定
    └── vue.config.js
```

## 設定ファイル

### ルートレベル設定ファイル

| ファイル | 説明 | Git管理 |
|---------|------|---------|
| `.env` | 環境変数設定 | ✗ |
| `.env.example` | 環境変数テンプレート | ✓ |
| `.editorconfig` | エディタ設定 | ✓ |
| `.eslintrc.js` | ESLint設定 | ✓ |
| `.gitignore` | Git除外設定 | ✓ |
| `.php-cs-fixer.php` | PHP CS Fixer設定 | ✓ |
| `.prettierrc` | Prettier設定 | ✓ |
| `.stylelintrc.js` | Stylelint設定 | ✓ |
| `composer.json` | PHP依存関係 | ✓ |
| `composer.lock` | PHP依存関係ロック | ✓ |
| `docker-compose.dev.yml` | Docker Compose設定 | ✓ |
| `package.json` | Node.js依存関係 | ✓ |
| `yarn.lock` | Yarn依存関係ロック | ✓ |
| `tsconfig.json` | TypeScript設定 | ✓ |

## Dockerコンテナ構成

| コンテナ名 | 用途 | 主な操作 |
|-----------|------|---------|
| `homes-kaigo/php` | PHP 7.2環境 | Composerコマンド実行 |
| `homes-kaigo/node-js` | Node.js環境 | Yarn/NPMコマンド実行、CSS/JSトランスパイル |

### Docker起動コマンド

```bash
docker compose -f docker-compose.dev.yml up
```

## Git管理対象外ファイル・ディレクトリ

以下は`.gitignore`で除外されています：

- `.env` - 環境変数（個人設定）
- `vendor/` - Composer依存パッケージ
- `.composer/` - Composer設定
- `node_modules/` - Node.js依存パッケージ
- `docker/settings/certs/netskope-root-ca.crt` - 証明書
- `var/cache/` - キャッシュファイル
- `var/logs/` - ログファイル

## 開発ワークフロー

### 1. PHP依存関係の更新

```bash
# Docker PHPコンテナに接続
docker exec -it homes-kaigo-php bash

# Composerパッケージ更新
composer update --optimize-autoloader [パッケージ名]

# Autoloadの最適化のみ
composer dump-autoload --optimize
```

注意：`vendor/bin/`、`.composer/`ディレクトリはcommit前に削除

### 2. Node.js依存関係の更新

```bash
# Docker Node.jsコンテナに接続
docker exec -it homes-kaigo-node-js bash

# 初回セットアップ
yarn install

# CSS/JSトランスパイル
yarn build
```

### 3. 環境変数の管理

1. `.env.example`をコピーして`.env`を作成
2. `.env`に個人設定を記述
3. `.env.example`が更新されたら、`.env`に反映

## アプリケーション構成

### エントリーポイント

| アプリケーション | 開発環境 | 本番環境 |
|----------------|---------|---------|
| 管理画面 | `htdocs/kaigo_admin/app_dev.php` | `htdocs/kaigo_admin/app.php` |
| PC版フロント | `htdocs/kaigo_pc/app_dev.php` | `htdocs/kaigo_pc/app.php` |
| SP版フロント | `htdocs/kaigo_sp/app_dev.php` | `htdocs/kaigo_sp/app.php` |

### 環境設定

各アプリケーションは以下の環境をサポート：

- `dev` - 開発環境
- `test` - テスト環境
- `pool` - プール環境
- `backstage` - バックステージ環境
- `live` - 本番環境

設定ファイルは`apps/[アプリ名]/config/config_[環境].yml`に配置

## 外部ディレクトリ

### ~/Projects/

開発プロジェクトを格納する推奨ディレクトリ

- **場所**: `~/Projects/`
- **用途**: GitHubからクローンしたリポジトリの配置
- **作成**: `mkdir ~/Projects`

## 参考資料

- [README.md](./README.md) - 環境構築の詳細手順
- Confluence記事（社内ドキュメント）
  - 5-3-0. 事前準備
  - 5-3-1. 公開鍵作成
  - 5-3-2. SSH設定
  - 5-3-6. 開発環境への同期


## よくある質問（FAQ）

### Q1: どのディレクトリから見ればいいですか？

**A**: 初学者は以下の順番で見ることをおすすめします：

1. `README.md` - 環境構築手順を理解
2. `apps/kaigo_pc_front/config/` - アプリケーション設定を確認
3. `src/Kaigo/PcFrontBundle/Controller/` - 画面の処理を理解
4. `src/LifullKaigo/FacilityReviewBundle/` - 具体的な機能を学ぶ
5. `assets_webpack/scripts/pc/` - フロントエンドのコードを見る

### Q2: 新しい画面を追加するには？

**A**: 以下の手順で追加します：

1. **Controllerを作成**: `src/Kaigo/PcFrontBundle/Controller/NewPageController.php`
2. **ルーティング設定**: `apps/kaigo_pc_front/config/routing.yml`
3. **Twigテンプレート作成**: `src/Kaigo/PcFrontBundle/Resources/views/NewPage/index.html.twig`
4. **CSS/JS追加**: `assets_webpack/styles/pc/` と `assets_webpack/scripts/pc/`
5. **ビルド実行**: `yarn build`

### Q3: データベースのテーブルを変更するには？

**A**: マイグレーションSQLを作成します：

1. `docs/postgresql/migrations/` に新しいSQLファイルを作成
2. ALTER TABLE文を記述
3. 対応するEntityクラスを更新
4. Repositoryクラスを更新（必要に応じて）

### Q4: 外部APIと連携するには？

**A**: 以下の手順で実装します：

1. 新しいBundleを作成（例：`ExternalApiBundle`）
2. Serviceクラスを作成してAPI呼び出しロジックを実装
3. Guzzleなどのライブラリを使用
4. `.env`にAPI認証情報を設定

### Q5: テストはどこに書きますか？

**A**: `tests/` ディレクトリに配置します：

```
tests/
├── Unit/              # 単体テスト
│   └── Services/
├── Functional/        # 機能テスト
│   └── Controller/
└── Integration/       # 統合テスト
```

### Q6: ログはどこで確認できますか？

**A**: 以下の場所にログが出力されます：

- アプリケーションログ: `var/logs/[環境]_[日付].log`
- Symfonyデバッグログ: `var/logs/dev.log`
- エラーログ: `var/logs/error.log`

```bash
# ログをリアルタイムで確認
tail -f var/logs/dev.log
```

### Q7: キャッシュをクリアするには？

**A**: 以下のコマンドを実行します：

```bash
# Symfonyキャッシュクリア
php apps/kaigo_pc_front/console cache:clear --env=dev

# 手動でキャッシュディレクトリを削除
rm -rf var/cache/*

# Redisキャッシュクリア（必要に応じて）
redis-cli FLUSHALL
```

## 詳細な開発ワークフロー

### 新機能開発の典型的な流れ

**例：施設レビュー機能に「いいね」ボタンを追加する**

**ステップ1: データベース設計**
```bash
# マイグレーションSQLを作成
vi docs/postgresql/migrations/facility/add_like_count.sql
```

**ステップ2: Entityを更新**
```bash
# EntityクラスにlikeCountプロパティを追加
vi src/LifullKaigo/FacilityReviewBundle/Entity/FacilityReview.php
```

**ステップ3: Serviceを作成**
```bash
# いいねを処理するサービスを作成
vi src/LifullKaigo/FacilityReviewBundle/Services/LikeService.php
```

**ステップ4: Controllerを更新**
```bash
# いいねボタンのエンドポイントを追加
vi src/Kaigo/PcFrontBundle/Controller/FacilityReviewController.php
```

**ステップ5: フロントエンド実装**
```bash
# TypeScriptでいいねボタンの処理を実装
vi assets_webpack/scripts/pc/facility_review.ts

# SCSSでボタンのスタイルを追加
vi assets_webpack/styles/pc/object/components/_like_button.scss

# ビルド実行
yarn build
```

**ステップ6: テンプレート更新**
```bash
# Twigテンプレートにボタンを追加
vi src/Kaigo/PcFrontBundle/Resources/views/FacilityReview/detail.html.twig
```

**ステップ7: 動作確認**
```bash
# ブラウザでアクセス
http://localhost/kaigo_pc/app_dev.php/facility/review/123
```

### よくあるトラブルシューティング

**問題1: 「Class not found」エラー**
```bash
# 解決方法: Autoloadを再生成
composer dump-autoload --optimize
```

**問題2: CSSが反映されない**
```bash
# 解決方法: Webpackで再ビルド
yarn build

# キャッシュをクリア
rm -rf var/cache/*
```

**問題3: Dockerコンテナが起動しない**
```bash
# 解決方法: コンテナを再作成
docker compose -f docker-compose.dev.yml down
docker compose -f docker-compose.dev.yml up --build
```

**問題4: データベース接続エラー**
```bash
# 解決方法: .envファイルの接続情報を確認
cat .env | grep DATABASE

# PostgreSQLが起動しているか確認
ps aux | grep postgres
```

## アプリケーション間の関係図

```
┌─────────────────────────────────────────────┐
│          ユーザー（ブラウザ）                │
└─────────────────────────────────────────────┘
         │              │              │
         ↓              ↓              ↓
┌──────────────┐ ┌──────────────┐ ┌──────────────┐
│  PC版サイト   │ │ スマホ版サイト│ │  管理画面     │
│ kaigo_pc_front│ │ kaigo_sp_front│ │ kaigo_admin  │
└──────────────┘ └──────────────┘ └──────────────┘
         │              │              │
         └──────────────┴──────────────┘
                        ↓
              ┌──────────────────┐
              │   共通ロジック    │
              │  (src/Kaigo/     │
              │   DomainBundle)  │
              └──────────────────┘
                        ↓
              ┌──────────────────┐
              │  機能バンドル     │
              │ (LifullKaigo/*)  │
              └──────────────────┘
                        ↓
              ┌──────────────────┐
              │  データベース     │
              │  (PostgreSQL)    │
              └──────────────────┘
```

## 参考資料

### 社内ドキュメント

- README.md - 環境構築の詳細手順
- Confluence記事
  - 5-3-0. 事前準備
  - 5-3-1. 公開鍵作成
  - 5-3-2. SSH設定
  - 5-3-6. 開発環境への同期

### 外部ドキュメント

- [Symfony 3.4 公式ドキュメント](https://symfony.com/doc/3.4/index.html)
- [Doctrine ORM ドキュメント](https://www.doctrine-project.org/projects/doctrine-orm/en/latest/)
- [Twig テンプレートエンジン](https://twig.symfony.com/doc/2.x/)
- [Webpack 公式ガイド](https://webpack.js.org/guides/)
- [TypeScript ハンドブック](https://www.typescriptlang.org/docs/)

### 学習リソース

**初心者向け**:
1. Symfonyの基本概念を理解する
2. MVCアーキテクチャを学ぶ
3. Doctrineでのデータベース操作を学ぶ
4. Twigテンプレートの書き方を学ぶ

**中級者向け**:
1. Symfonyのサービスコンテナを理解する
2. イベントリスナーの使い方を学ぶ
3. カスタムバンドルの作成方法を学ぶ
4. パフォーマンスチューニングを学ぶ
