# 必要条件（Requirements）
MobSF をローカルにセットアップするための必要条件です。

システム要件: RAM 8GB 以上、CPU 3GHz、空きディスク容量 80GB 以上

対応 OS: Mac、Linux、Windows

## Static Analysis（静的解析）
* **Mac**

  * Git をインストールする
    
    `brew install git`
  * [Python **3.12+**](https://github.com/pyenv/pyenv) をインストールする
  * [OpenJDK 21+](https://jdk.java.net/) をインストールし、環境変数 `JAVA_HOME` を設定する
  * コマンドラインツールをインストールする
    
    ```bash
    xcode-select --install
    sudo xcode-select --switch /Applications/Xcode.app
    ```
  * [wiki の手順](https://github.com/JazzCore/python-pdfkit/wiki/Installing-wkhtmltopdf) に従い [wkhtmltopdf](https://wkhtmltopdf.org/downloads.html) をインストールする
  * Windows アプリの Static Analysis には、Mac / Linux 上でも Windows ホストまたは Windows VM が必要です。[詳細](https://github.com/MobSF/Mobile-Security-Framework-MobSF/blob/master/mobsf/install/windows/readme.md)


* **Ubuntu/Debian based Linux**

  * Git をインストールする
    
    `sudo apt install git -y`
  * [Python **3.12+**](https://github.com/pyenv/pyenv) をインストールする
  * OpenJDK 21+ をインストールし、環境変数 `JAVA_HOME` を設定する
    
    `sudo apt install openjdk-21-jdk -y`
  * 次の依存パッケージをインストールする:
    ```bash
    sudo apt install python3-dev python3-venv python3-pip build-essential \
      libffi-dev libssl-dev libxml2-dev libxslt1-dev \
      libjpeg8-dev zlib1g-dev wkhtmltopdf -y
    ```

  * Windows アプリの Static Analysis には、Mac / Linux 上でも Windows ホストまたは Windows VM が必要です。[詳細](https://github.com/MobSF/Mobile-Security-Framework-MobSF/blob/master/mobsf/install/windows/readme.md)

* **Windows**

  * [Git](https://git-scm.com/download/win) をインストールする
  * [Python **3.12+**](https://www.python.org/) をインストールする
  * [OpenJDK 21+](https://www.microsoft.com/openjdk) をインストールし、環境変数 `JAVA_HOME` を設定する
  * [Microsoft Visual C++ Build Tools](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) をインストールする
  * [OpenSSL (non-light)](https://slproweb.com/products/Win32OpenSSL.html) をインストールする
  * [wiki の手順](https://github.com/JazzCore/python-pdfkit/wiki/Installing-wkhtmltopdf) に従い [wkhtmltopdf](https://wkhtmltopdf.org/downloads.html) をダウンロードしてインストールする
  * `wkhtmltopdf` バイナリがあるフォルダを環境変数 PATH に追加する


?> **IMPORTANT:** iOS IPA Analysis は **Mac、Linux、Docker コンテナ** でのみ動作します。

***

# インストール（Installation）

**動作確認済み: Windows 10、Ubuntu 22.04、macOS 14.3 (M3)**

!> 先に記載の必要条件をすべてインストールしておいてください。

```bash
# Clone the MobSF repo
git clone https://github.com/MobSF/Mobile-Security-Framework-MobSF.git
cd Mobile-Security-Framework-MobSF

# Linux or Mac
./setup.sh

# Windows
setup.bat
```

?> Windows ユーザーは、`setup.bat` を実行する前に、MobSF のフォルダを開いているエクスプローラーや、MobSF を開いているテキストエディタをすべて閉じてください。これらが残っていると権限エラーでセットアップが中断されることがあります。

***

# MobSFの起動（Running MobSF）

```bash
# Linux or Mac
./run.sh 127.0.0.1:8000

# Windows
run.bat 127.0.0.1:8000
``` 

!> 引数なしで run スクリプトを実行すると、MobSF サーバーは `0.0.0.0:8000` で待ち受けます。

ブラウザで `http://localhost:8000/` を開くと、MobSF の Web インターフェースにアクセスできます。デフォルトの認証情報は `mobsf/mobsf` です。
***

# 非同期スキャンキュー（Asynchronous Scan Queues）

MobSF は非同期タスクキューにも対応しています。

```bash
# Linux or Mac
MOBSF_ASYNC_ANALYSIS=1 ./run.sh

# Windows
set MOBSF_ASYNC_ANALYSIS=1
run.bat

# Run DjangoQ2 cluster to accept scan jobs.
poetry run python manage.py qcluster
```

# Dynamic Analysis（動的解析）

Dynamic Analysis には、次のいずれかの Android / iOS ターゲットが必要です。

* [Genymotion Desktop](https://www.genymotion.com/download/)
* [Genymotion Cloud](https://www.genymotion.com/cloud/)
* [Android Studio Emulator](https://developer.android.com/studio)
* [Corellium Android](https://support.corellium.com/devices/android) 
* [Corellium iOS](https://support.corellium.com/devices/ios)
* [Jailbroken iOS device](#jailbroken-ios-device)

!> **jailbreak されていない**実機での Dynamic Analysis はサポートされていません。Android では rooted な実機での Dynamic Analysis は可能ですが、**公式にはサポートされていません。**

## Genymotion Android
?> 対応アーキテクチャ: arm64、x86、x86_64。Android **4.1 - 11.0**（最大 **API 30**）

Genymotion は、もっとも手間なくセットアップできる推奨の Dynamic Analysis 環境です。MobSF を起動する**前に** Genymotion の Android VM を起動してください。実行時に自動で設定されます。**Android 7.0** 以上を推奨します。

* **Android 5.0 - 11.0** — **Frida** を使用し、追加設定なしでそのまま動作します。
* **Android 4.1 - 4.4** — **Xposed Framework** を使用します。初回の Dynamic Analysis 前にランタイムを MobSFy する必要があります。Xposed Modules インストール後は VM の再起動も必要です。

Android Dynamic Analyzer ページの **MobSFy Android Runtime** ボタンをクリックして、Android ランタイム環境を MobSFy してください。

![MobSFy](https://user-images.githubusercontent.com/4301109/77839885-11033780-714f-11ea-9d52-df7b0bd314a0.png)

**HTTPS Proxy**

* Android **4.4 - 11.0** では、グローバルプロキシ設定が実行時に自動適用されます。
* Android **4.1 - 4.3** では、Dynamic Analyzer ページに表示される内容どおりに Android VM のプロキシを設定してください。

MobSF Dynamic Analyzer が Android デバイスを検出しない場合は、MobSF 起動前に環境変数 `MOBSF_ANALYZER_IDENTIFIER` でデバイス識別子を設定してください。

例: `MOBSF_ANALYZER_IDENTIFIER='192.168.56.101:5555'`。
Android デバイスの IP は Genymotion のタイトルバーで確認できます。デフォルトポートは `5555` です。

![Dynamic Analyzer IP](https://user-images.githubusercontent.com/4301109/65379210-0b312300-dce2-11e9-8827-f63d3b95dfd1.png)

## Android Studio Emulator
?> 対応アーキテクチャ: arm、arm64、x86、x86_64。Android **5.0 - 11.0**（最大 **API 30**）

Android Studio を最新版にインストールまたはアップグレードしてください。Google Play Store 付きの Android Emulator イメージは production イメージとみなされ、MobSF では使えません。

Google Play Store **なし**の Android Virtual Device（AVD）を作成してください。

!> **production**（非 rooted）イメージを選んではいけません。MobSF には、Google Play Store がプリインストールされていない rooted イメージが必要です。

![Create AVD](https://github.com/user-attachments/assets/5871ff00-2386-4f32-be37-1d749825b069)


AVD 作成後、一度起動して問題ないことを確認してください。起動中なら AVD と Android Studio を終了してください。

**Android Virtual Device（AVD）の起動**

MobSF を起動する**前に**、`scripts/start_avd.sh` または `scripts/start_avd.ps1` で AVD を起動してください。

```bash
# Run the script to list out available AVDs
scripts/start_avd.sh 
Available AVDs:

Medium_Phone_API_35
Pixel_5_API_30
Pixel_6a_API_29

Use any Android AVD 5.0 - 11.0, up to API 30 without Google Play (production image).
Usage: scripts/start_avd.sh AVD_NAME [START_PORT] [open_gapps.zip path]
Example: scripts/start_avd.sh Pixel_6_Pro_API_28 5554 /path/to/open_gapps.zip

# Choose the AVD you created, make sure it is a non-production AVD.
# MobSF will not work with production AVDs.

scripts/start_avd.sh Pixel_5_API_30
```

実行時に自動で設定されます。MobSF の Dynamic Analysis には AVD バージョン **5.0 から 11.0** が必要です。

**HTTPS Proxy**

* Android **5.0 - 8.0** では、MobSF はグローバルプロキシ設定を試みますが、adb の不具合で失敗することがあります。その場合は手動でプロキシを設定してください。
* Android **9.0** 以上では、グローバルプロキシ設定が実行時に自動適用されます。

**AVD 上の GApps（任意）**

Google Play Store が必要な場合は、<https://opengapps.org/> から適切なパッケージをダウンロードしてください。
`start_avd` スクリプトに GApps の zip パスを渡して実行します。Windows では現在未対応のため、必要な手順を手動で行う必要があります。

```bash
scripts/start_avd.sh Pixel_6a_API_29 5554 ~/Downloads/open_gapps-arm64-10.0-stock-20220215.zip
```

MobSF Dynamic Analyzer が Android デバイスを検出しない場合は、エミュレータのシリアル番号を確認してください。

![Android AVD](https://github.com/MobSF/Mobile-Security-Framework-MobSF/assets/4301109/e9e849b6-69ad-47a4-8693-c75a0e1aa7cb)

MobSF 起動前に、環境変数 `MOBSF_ANALYZER_IDENTIFIER` でデバイス識別子を設定できます。

例: `MOBSF_ANALYZER_IDENTIFIER='emulator-5554'`


## Genymotion Cloud Android
?> 対応アーキテクチャ: arm64、x86、x86_64。Android **5.1 - 11.0**（最大 **API 30**）

クラウド上で Genymotion の Android VM を起動し、MobSF を起動する**前に** adb で接続してください。実行時に自動で設定されます。**Android 7.0** 以上を推奨します。

このドキュメントでは Amazon Web Services（AWS）を例にします。Genymotion Cloud SaaS、Microsoft Azure、Google Cloud Platform、Alibaba Cloud でも同様の手順に従ってください。

1. [Genymotion AMI](https://aws.amazon.com/marketplace/seller-profile?id=933724b4-d35f-4266-905e-e52e4792bc45) で EC2 インスタンスを起動する

![AWS Genymotion AMI](https://user-images.githubusercontent.com/4301109/81505732-7bb3a100-92bf-11ea-9ba5-b1899810db2e.png)

2. AMI の **Security Group** を変更し、ポート **5555** への inbound TCP を許可する。Genymotion Cloud VM へのリモート adb 接続に必要です。

![Allow ADB Connection](https://user-images.githubusercontent.com/4301109/81505878-9b979480-92c0-11ea-9456-32cf5254d381.png)

3. Public IP にアクセスして Genymotion Cloud VM を開く。デフォルトのユーザー名は `genymotion`、パスワードは EC2 インスタンス ID です。
[詳細](https://docs.genymotion.com/paas/8.0/02_Getting_Started/021_AWS.html#create-and-set-up-an-instance)

4. Configuration を開き、ADB を有効にする

![Enable ADB in Genymotion Cloud](https://user-images.githubusercontent.com/4301109/81505975-46a84e00-92c1-11ea-82a5-8912f96849b1.png)

5. ローカルマシンから、adb で Genymotion Cloud VM に接続できることを確認する。

```bash
adb connect <public_ip>:5555
adb devices
```
![ADB connect](https://user-images.githubusercontent.com/4301109/81506018-9be45f80-92c1-11ea-8486-fcac8daee7be.png)

6. これで AWS 上の Genymotion Cloud VM を使って MobSF Dynamic Analysis を実行できます。

MobSF Dynamic Analyzer が Cloud VM を検出しない場合は、MobSF 起動前に環境変数 `MOBSF_ANALYZER_IDENTIFIER` でデバイス識別子を設定してください。

例: `MOBSF_ANALYZER_IDENTIFIER='<public_ip>:5555'`。

MobSF が adb を検出できない場合は、`<user_home_dir>/.MobSF/config.py` で `ADB_BINARY` を設定してください。

例: `ADB_BINARY='/Applications/Genymotion.app/Contents/MacOS/tools/adb'`。

## Corellium Android VM

?> 対応: **rooted userdebug** ビルド、arm64、Android **7.1.2 - 11.0**（最大 **API 30**）

!> 非 rooted の **user** ビルドを選んではいけません。MobSF には rooted の **userdebug** ビルドが必要です。

1. 対応する rooted **userdebug** Android デバイスを作成したら、Corellium の `Connect via VPN` 手順に従う。

![Corellium Android](https://github.com/MobSF/Mobile-Security-Framework-MobSF/assets/4301109/f384421c-98af-47b1-8d98-29641d9ca974)

2. 提供された VPN 設定を使って Corellium ネットワークに接続する。

3. ホストから `adb connect` を実行し、接続できることを確認する。

![Corellium adb](https://github.com/MobSF/Mobile-Security-Framework-MobSF/assets/4301109/c6f1135e-b1ef-4a14-b9bf-6ebfab2e3cca)

4. MobSF 起動前に、環境変数 `MOBSF_ANALYZER_IDENTIFIER` を Corellium Android デバイスの `<private_ip>:<port>` に設定する（例: `10.11.1.1:5001`）。


## Corellium iOS VM

MobSF v4.4.0 以降では、Corellium iOS VM に Frida Server >17.0.0 がインストールされている必要があります。

!> jailbreak されていないデバイスは MobSF では使えません。物理 iPhone を使う場合は jailbreak 済みデバイスを使用してください。

1. Corellium アカウントを用意したら、https://app.corellium.com/profile/api から API key を作成する。

![Corellium API](https://user-images.githubusercontent.com/4301109/289017703-b6f25054-d1b5-4c0e-a781-68b18260fb6a.png)

2. API key を環境変数 `MOBSF_CORELLIUM_API_KEY` に設定する。別ドメインの Corellium エンタープライズ版を使う場合は、環境変数 `MOBSF_CORELLIUM_API_DOMAIN` に正しいドメインも設定してください。

3. MobSF の HTTPS プロキシを有効にするには、iOS VM 側でプロキシを設定します。iPhone の `Settings` → `Wi-Fi` → `Corellium` Wi‑Fi を選択 → 下へスクロールして `Configure Proxy` → `Manual configuration` を選択 → `Server` に `127.0.0.1`、`Port` に `1337` を設定 → `Save`。

![iOS HTTPS Proxy](https://user-images.githubusercontent.com/4301109/289017713-ffc54f0e-1c23-484d-a612-0318ad41e7a3.png)

4. MobSF を起動すると、Corellium 上で jailbroken iOS VM を作成・管理して Dynamic Analysis を行えます。

## Jailbroken iOS device

物理の jailbreak 済みデバイスと SSH、互換性のある **Frida server**（MobSF v4.5.1 以降では例として **>17.0.0**）を使用します。

0. iPhone を **USB** で接続するホストに **libimobiledevice** をインストールし、`iproxy` および `idevice*` ツールを使えるようにする（USB ポート転送とデバイス確認用）。**macOS:** `brew install libimobiledevice`。**Debian/Ubuntu:** `sudo apt install libimobiledevice-utils`。
1. OpenSSH（または jailbreak 側の SSH）に到達でき、下記で設定する認証情報と一致することを確認する。
2. **`MOBSF_IOS_ANALYZER_IDENTIFIERS`** を SSH ソケット（`IP:PORT`）に設定する。例: `192.168.1.100:22`、複数デバイス: `192.168.1.100:22,192.168.1.101:22`。
3. デフォルト（`root` / `alpine`）と異なる場合は、任意で **`MOBSF_IOS_SSH_USER`** と **`MOBSF_IOS_SSH_PASSWORD`** を設定する。
4. できれば MobSF ホストと iPhone を同じ Wi‑Fi / LAN に接続する。MobSF の HTTPS プロキシを有効にするには、iOS デバイス側でプロキシを設定します。iPhone の `Settings` → `Wi-Fi` → Wi‑Fi SSID を選択 → 下へスクロールして `Configure Proxy` → `Manual configuration` を選択 → `Server` に `<ip address of mobsf host>`、`Port` に `1337` を設定 → `Save`。

***

# MobSFの更新（Updating MobSF）

```bash
cd Mobile-Security-Framework-MobSF/
git pull origin master
poetry update
poetry run python manage.py makemigrations
poetry run python manage.py makemigrations StaticAnalyzer
poetry run python manage.py migrate
poetry run python manage.py create_roles
# For Linux/Mac
poetry run python mobsf/MobSF/tools_download.py ~/.MobSF
DJANGO_SUPERUSER_PASSWORD=mobsf poetry run python manage.py createsuperuser --noinput --username "mobsf" --email ""
# For Windows
poetry run python mobsf/MobSF/tools_download.py %USERPROFILE%\.MobSF
set DJANGO_SUPERUSER_PASSWORD=mobsf && poetry run python manage.py createsuperuser --noinput --username "mobsf" --email ""
```

!> データベースマイグレーションに失敗した場合は、MobSF ディレクトリ（Linux / Mac では `~/.MobSF`、Windows では `C:\Users\<user>\.MobSF`）を削除し、セットアップスクリプト（Linux / Mac は `setup.sh`、Windows は `setup.bat`）を再実行してください。これまでのスキャン結果とデータはすべて削除されます。
***

# テストの実行（Running Tests）

テスト実行には tox を使用します。
```bash
pip install tox

#For linting
tox -e lint

# For running lint + test
tox -e lint,test
```
