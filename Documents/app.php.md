---
category:
  - lifull-kaigo
---
このコードは **Symfonyアプリケーションのフロントコントローラー**（エントリーポイント）で、URLに応じて適切なアプリケーションを起動する処理を行っています。

## 処理の流れ

### 1. SSL対応処理
```php
if (!empty($_SERVER['HTTP_X_HOMES_SSL']) && strtolower($_SERVER['HTTP_X_HOMES_SSL']) !== 'off') {
    $_SERVER['HTTPS'] = 443;
}
```
- ロードバランサー経由のSSL接続を検知
- `HTTP_X_HOMES_SSL` ヘッダーがあれば、HTTPS接続として扱う

### 2. 初期化
```php
require __DIR__ . '/../../vendor/autoload.php';
$dotenv = new Dotenv();
$dotenv->load(__DIR__ . '/../../apps/_kaigo_common/config/.env');
```
- Composerのオートローダーを読み込み
- 環境変数ファイル（.env）を読み込み

### 3. URLルーティング判定
```php
$requestPaths = explode('/', trim($_SERVER['REQUEST_URI'] ?? '', '/'));
$firstPath = $requestPaths ? $requestPaths[0] : '';
```
- リクエストURLを `/` で分割
- 例: `/manager/users` → `['manager', 'users']`

### 4. アプリケーション選択
```php
$appName = 'kaigo_pc_front'; // デフォルト
switch ($firstPath) {
    case '_ping.status':
        die('OK'); // ヘルスチェック用
    case 'manager':
        $appName = 'kaigo_manager'; // 管理画面
        break;
    case 'api':
        if (in_array(($requestPaths[1] ?? ''), ['fusion', 'article'])) {
            $appName = 'kaigo_api'; // API
        }
        break;
    case 'client':
        $appName = (($requestPaths[1] ?? '') === 'manager') 
            ? 'kaigo_pc_client_manager' 
            : 'kaigo_pc_front';
        break;
}
```

**URLとアプリケーションの対応:**
- `/_ping.status` → ヘルスチェック（即座に "OK" を返す）
- `/manager/*` → 管理画面アプリ
- `/api/fusion/*` または `/api/article/*` → APIアプリ
- `/client/manager/*` → クライアント管理画面
- その他 → フロントアプリ（デフォルト）

### 5. Symfonyカーネル起動
```php
$rootDir = $projectDir . '/apps/' . $appName;
require $rootDir . '/AppKernel.php';

$env = basename(dirname($projectDir)); // 環境名（dev, stg, prd等）
$debug = false;

$kernel = new AppKernel($env, $debug);
```
- 選択されたアプリケーションのカーネルを読み込み
- 環境名はプロジェクトディレクトリの親ディレクトリ名から取得

### 6. リクエスト処理
```php
$request = Request::createFromGlobals();
$response = $kernel->handle($request);
$response->send();
$kernel->terminate($request, $response);
```
- HTTPリクエストを作成
- カーネルで処理してレスポンスを生成
- レスポンスをクライアントに送信
- 後処理（ログ記録等）を実行

## まとめ

このファイルは **1つのエントリーポイントで複数のSymfonyアプリケーションを切り替える** マルチアプリケーション構成を実現しています。URLの最初のパスを見て、どのアプリケーションを起動するかを動的に決定する仕組みです。