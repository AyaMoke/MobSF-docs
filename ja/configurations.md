# MobSF Configurations（設定）

MobSF は、動作のカスタマイズやさまざまなデプロイ形態への対応のため、環境変数による設定をサポートしています。
対応している環境変数の一覧です。

## General（一般）
- **`MOBSF_DEBUG`**: `1` に設定するとデバッグモードを有効にします。
- **`MOBSF_SECRET_KEY`**: 静的な Django secret を設定します。
- **`MOBSF_USE_X_FORWARDED_HOST`**: `1` に設定すると `X-Forwarded-Host` ヘッダー対応を有効にします。
- **`MOBSF_USE_X_FORWARDED_PORT`**: `1` に設定すると `X-Forwarded-Port` ヘッダー対応を有効にします。
- **`TIME_ZONE`**: サーバーのタイムゾーンを設定します。デフォルトは `UTC` です。
- **`MOBSF_PLATFORM`**: MobSF の実行環境を指定します（例: `docker`）。
- **`MOBSF_HOME_DIR`**: 解析データを保存する MobSF ホームディレクトリを指定します。デフォルトは `~/` または `$HOME` です。

## Database Configuration（データベース）
必要な環境変数が設定されている場合のみ PostgreSQL が構成されます。未設定の場合、MobSF は sqlite3 データベースを使用します。
- **`POSTGRES_USER`**: PostgreSQL データベースのユーザー名。
- **`POSTGRES_PASSWORD`**: PostgreSQL データベースのパスワード。
- **`POSTGRES_PASSWORD_FILE`**: PostgreSQL パスワードを格納したファイルのパス（docker secrets マウント）。
- **`POSTGRES_HOST`**: PostgreSQL サーバーのホスト名または IP アドレス。
- **`POSTGRES_PORT`**: PostgreSQL サーバーへの接続ポート（デフォルト: `5432`）。
- **`POSTGRES_DB`**: PostgreSQL データベース名（デフォルト: `mobsf`）。

## Asynchronous Scan Queue（非同期スキャンキュー）
- **`MOBSF_ASYNC_ANALYSIS`**: `1` に設定すると非同期解析を有効にします。DjangoQ2 による非同期タスクキュー対応に使用します。
- **`MOBSF_ASYNC_ANALYSIS_TIMEOUT`**: 非同期タスクのタイムアウト（分）。デフォルトは 60 分です。
- **`MOBSF_ASYNC_WORKERS`**: 同時に実行できる非同期スキャン数。デフォルトは 3 workers です。
- **`MOBSF_MULTIPROCESSING`**: マルチプロセシングモードを指定します（`billiard`、`thread`、`default`）。

## Tool Timeouts（ツールのタイムアウト）
- **`MOBSF_JADX_TIMEOUT`**: JADX / apktool 操作のタイムアウト秒数（デフォルト: `1000` 秒）。
- **`MOBSF_SAST_TIMEOUT`**: 静的解析のタイムアウト秒数（デフォルト: `1000` 秒）。
- **`MOBSF_BINARY_ANALYSIS_TIMEOUT`**: バイナリ解析のタイムアウト秒数（デフォルト: `600` 秒）。

## Authentication and Rate Limiting（認証とレート制限）
- **`MOBSF_DISABLE_AUTHENTICATION`**: 設定すると認証を無効にします。
- **`MOBSF_RATELIMIT`**: API リクエストのレート制限（デフォルト: `7/m`）。
- **`MOBSF_API_ONLY`**: `1` に設定すると REST API のみのモードになります。Web UI エンドポイントは無効になります。
- **`MOBSF_API_KEY`**: MobSF REST API 用のカスタム静的認証キーを設定します。
- **`MOBSF_API_KEY_FILE`**: REST API 認証キーをファイルから読み取ります（docker secrets マウント）。

## Proxy Configuration（プロキシ）
- **`MOBSF_PROXY_IP`**: MobSF（httptools）が起動する HTTPS プロキシの IP アドレス（デフォルト: `127.0.0.1`）。
- **`MOBSF_PROXY_PORT`**: MobSF（httptools）が起動する HTTPS プロキシのポート（デフォルト: `1337`）。

## Upstream Proxy Settings（上流プロキシ）
- **`MOBSF_UPSTREAM_PROXY_ENABLED`**: `1` に設定すると上流プロキシ対応を有効にします。
- **`MOBSF_UPSTREAM_PROXY_SSL_VERIFY`**: `1` に設定すると上流プロキシの SSL 検証を行います。
- **`MOBSF_UPSTREAM_PROXY_TYPE`**: 上流プロキシの種別（デフォルト: `http`）。
- **`MOBSF_UPSTREAM_PROXY_IP`**: 上流プロキシの IP アドレス（デフォルト: `127.0.0.1`）。
- **`MOBSF_UPSTREAM_PROXY_PORT`**: 上流プロキシのポート（デフォルト: `3128`）。
- **`MOBSF_UPSTREAM_PROXY_USERNAME`**: 上流プロキシ認証のユーザー名。
- **`MOBSF_UPSTREAM_PROXY_PASSWORD`**: 上流プロキシ認証のパスワード。

## Static Analysis Configuration（静的解析）
- **`MOBSF_DOMAIN_MALWARE_SCAN`**: ドメインマルウェアスキャンを有効にします。デフォルトは `1` です。
- **`MOBSF_APKID_ENABLED`**: APKiD スキャンを有効にします。デフォルトは `1` です。
- **`MOBSF_DYLIB_ANALYSIS_ENABLED`**: dylib 解析を有効にします。デフォルトは `1` です。
- **`MOBSF_SO_ANALYSIS_ENABLED`**: 共有オブジェクト解析を有効にします。デフォルトは `1` です。
- **`MOBSF_DEX2SMALI_ENABLED`**: Android バイナリの dex から smali への変換を有効にします。デフォルトは `1` です。
- **`MOBSF_PERM_MAPPING_ENABLED`**: Android スキャンでの permission とコードのマッピングを有効にします。デフォルトは `1` です。
- **`MOBSF_NIAP_ENABLED`**: `1` に設定すると NIAP スキャンを有効にします。デフォルトでは無効です。
- **`MOBSF_CVSS_SCORE_ENABLED`**: `1` に設定すると CVSSV2 スコアを表示します。デフォルトでは無効です。

## Dynamic Analysis Configuration（動的解析）

### Android
- **`MOBSF_ANALYZER_IDENTIFIER`**: Android Debug Bridge（adb）互換のデバイス識別子。

### Jailbroken iOS device
Corellium ではなく、SSH 経由（USB ポート転送または Wi‑Fi）で到達できる **物理の jailbreak 済み iOS デバイス** を対象にする場合に使用します。

- **`MOBSF_IOS_ANALYZER_IDENTIFIERS`**: SSH エンドポイントを `IP:PORT` 形式で指定します（カンマ区切りで複数可）。例: `192.168.1.100:22`。複数デバイス: `192.168.1.100:22,192.168.1.101:22`。
- **`MOBSF_IOS_SSH_USER`**: SSH ユーザー名（デフォルト: `root`）。
- **`MOBSF_IOS_SSH_PASSWORD`**: SSH パスワード（デフォルト: `alpine`）。

## Corellium iOS
- **`MOBSF_CORELLIUM_API_DOMAIN`**: Corellium 連携用の API ドメイン。
- **`MOBSF_CORELLIUM_API_KEY`**: Corellium 連携用の API key。
- **`MOBSF_CORELLIUM_PROJECT_ID`**: Corellium 連携用の Project ID（任意）。

### Common（共通）
- **`MOBSF_FRIDA_TIMEOUT`**: Frida 接続タイムアウト。デフォルトは `4` 秒です。
- **`MOBSF_ACTIVITY_TESTER_SLEEP`**: Activity を起動する前に待機する秒数。デフォルトは `4` 秒です。Activity tester で使用します。
- **`MOBSF_ADB/MOBSF_ADB_BINARY`**: Android Dynamic Analysis で MobSF が使用する `adb` バイナリのパスを指定します。

## VirusTotal Integration
- **`MOBSF_VT_ENABLED`**: `1` に設定すると VirusTotal 連携を有効にします。
- **`MOBSF_VT_API_KEY`**: VirusTotal 連携用の API key。
- **`MOBSF_VT_UPLOAD`**: `1` に設定すると VirusTotal へのファイルアップロードを有効にします。未設定の場合はハッシュ値のみ送信します。

## AppMonsta Integration
- **`MOBSF_APPMONSTA_API`**: パッケージ詳細取得用の AppMonsta API key

## SAML SSO Integration
- **`MOBSF_IDP_METADATA_URL`**: SAML IdP の Metadata URL。
- **`MOBSF_IDP_ENTITY_ID`**: SAML IdP の Entity ID。
- **`MOBSF_IDP_SSO_URL`**: SAML IdP の Single Sign-On（SSO）URL。
- **`MOBSF_IDP_X509CERT`**: SAML IdP の X.509 証明書。
- **`MOBSF_IDP_IS_ADFS`**: `1` に設定すると IdP を ADFS として扱います。
- **`MOBSF_SP_HOST`**: SAML Service Provider（SP）のホスト名。
- **`MOBSF_SP_ALLOW_PASSWORD`**: `1` に設定すると SAML SP でパスワードログインを有効にします。
- **`MOBSF_IDP_MAINTAINER_GROUP`**: 読み書き可能な Maintainer 用のカスタムグループ名。デフォルトは `Maintainer` です。
- **`MOBSF_IDP_VIEWER_GROUP`**: 読み取り専用 Viewer 用のカスタムグループ名。デフォルトは `Viewer` です。

## Custom binaries for Android SAST
- **`MOBSF_BUNDLE_TOOL`**: BundleTool バイナリのパス。
- **`MOBSF_JADX_BINARY`**: JADX バイナリのパス。
- **`MOBSF_BACKSMALI_BINARY`**: Backsmali バイナリのパス。
- **`MOBSF_VD2SVG_BINARY`**: VD2SVG バイナリのパス。
- **`MOBSF_APKTOOL_BINARY`**: apktool バイナリのパス。
- **`MOBSF_ADB_BINARY`**: ADB バイナリのパス。
- **`MOBSF_AAPT2_BINARY`**: AAPT2 バイナリのパス。
- **`MOBSF_AAPT_BINARY`**: AAPT バイナリのパス。
- **`MOBSF_JAVA_DIRECTORY`**: Java ディレクトリのパス。

#### Custom binaries for iOS SAST
- **`MOBSF_JTOOL_BINARY`**: jtool バイナリのパス。
- **`MOBSF_CLASSDUMP_BINARY`**: class-dump バイナリのパス。
- **`MOBSF_CLASSDUMP_SWIFT_BINARY`**: class-dump-swift バイナリのパス。

## Misc（その他）
- **`EFR_01`**: `1` に設定するとカスタムのエンタープライズ機能リクエストを有効にします。
