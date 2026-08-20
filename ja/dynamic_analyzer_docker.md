# Dynamic Analyzer

MobSF は、**特定の** rooted Android VM / エミュレータ、および jailbreak 済み iOS ターゲットをサポートします。

* [Genymotion Desktop](https://www.genymotion.com/download/)
* [Genymotion Cloud](https://www.genymotion.com/cloud/)
* [Android Studio Emulator](https://developer.android.com/studio)
* [Corellium Android](https://support.corellium.com/devices/android) 
* [Corellium iOS](https://support.corellium.com/devices/ios)
* [Jailbroken iOS device](#jailbroken-ios-device-beta)

## Genymotion Android
?> 対応アーキテクチャ: arm64、x86、x86_64。Android **4.1 - 11.0**（最大 **API 30**）

Genymotion は、もっとも手間なくセットアップできる推奨の Dynamic Analysis 環境です。MobSF を起動する**前に** Genymotion の Android VM を起動してください。**Android 7.0** 以上を推奨します。

* **Android 5.0 - 11.0** — **Frida** を使用し、追加設定なしでそのまま動作します。
* **Android 4.1 - 4.4** — **Xposed Framework** を使用します。初回の Dynamic Analysis 前にランタイムを MobSFy する必要があります。Xposed Modules インストール後は VM の再起動も必要です。

Android VM 起動後、タイトルバーからデバイス識別子を確認できます。

![Analyzer Identifier](https://github.com/MobSF/Mobile-Security-Framework-MobSF/assets/4301109/6204cdf4-1bc6-4b9a-a9f6-99db64c2f8e2)

MobSF の Docker イメージ起動時に、環境変数 `MOBSF_ANALYZER_IDENTIFIER` を VM のデバイス識別子に設定してください（例: `192.168.58.102:5555`）。

**HTTPS Proxy**

* Android **4.4 - 11.0** では、グローバルプロキシ設定が実行時に自動適用されます。
* Android **4.1 - 4.3** では、Dynamic Analyzer ページに表示される内容どおりに Android VM のプロキシを設定してください。


## Android Studio Emulator
?> 対応アーキテクチャ: arm、arm64、x86、x86_64。Android **5.0 - 11.0**（最大 **API 30**）

Android Studio を最新版にインストールまたはアップグレードしてください。Google Play Store 付きの Android Emulator イメージは production イメージとみなされ、**root 権限がない**ため MobSF では使えません。

Google Play Store **なし**の Android Virtual Device（AVD）を作成してください。

!> **production**（非 rooted）イメージを選んではいけません。MobSF には、Google Play Store がプリインストールされていない rooted イメージが必要です。

![Create AVD](https://github.com/user-attachments/assets/5871ff00-2386-4f32-be37-1d749825b069)


AVD 作成後、一度起動して問題ないことを確認してください。起動中なら AVD と Android Studio を終了してください。

!> 対応しているのは Android AVD **version 11、API 30** までです。それ以降の AVD は書き込み可能な `/system` を提供しないため、MobSF では動作しません。

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
エミュレータのシリアル番号を確認してください。この例では識別子は `emulator-5554` です。

![Android AVD](https://github.com/MobSF/Mobile-Security-Framework-MobSF/assets/4301109/e9e849b6-69ad-47a4-8693-c75a0e1aa7cb)


MobSF の Docker イメージ起動時に、環境変数 `MOBSF_ANALYZER_IDENTIFIER` を `emulator-5554` に設定してください。


**HTTPS Proxy**

* Android **5.0 - 8.0** では、MobSF はグローバルプロキシ設定を試みますが、adb の不具合で失敗することがあります。その場合は手動でプロキシを設定してください。
* Android **9.0** 以上では、グローバルプロキシ設定が実行時に自動適用されます。

**AVD 上の GApps（任意）**

Google Play Store が必要な場合は、<https://opengapps.org/> から適切なパッケージをダウンロードしてください。
`start_avd` スクリプトに GApps の zip パスを渡して実行します。Windows では現在未対応のため、必要な手順を手動で行う必要があります。

```bash
$ scripts/start_avd.sh Pixel_6a_API_29 5554 ~/Downloads/open_gapps-arm64-10.0-stock-20220215.zip
```

## Genymotion Cloud VM
?> 対応アーキテクチャ: arm64、x86、x86_64。Android **5.1 - 11.0**（最大 **API 30**）

MobSF を起動する**前に**、クラウド上で Genymotion の Android VM を起動してください。**Android 7.0** 以上を推奨します。

このドキュメントでは Amazon Web Services（AWS）を例にします。Genymotion Cloud SaaS、Microsoft Azure、Google Cloud Platform、Alibaba Cloud でも同様の手順に従ってください。

1. [Genymotion AMI](https://aws.amazon.com/marketplace/seller-profile?id=933724b4-d35f-4266-905e-e52e4792bc45) で EC2 インスタンスを起動する

![AWS Genymotion AMI](https://user-images.githubusercontent.com/4301109/81505732-7bb3a100-92bf-11ea-9ba5-b1899810db2e.png)

2. AMI の **Security Group** を変更し、ポート **5555** への inbound TCP を許可する。Genymotion Cloud VM へのリモート adb 接続に必要です。

![Allow ADB Connection](https://user-images.githubusercontent.com/4301109/81505878-9b979480-92c0-11ea-9456-32cf5254d381.png)

3. Public IP にアクセスして Genymotion Cloud VM を開く。デフォルトのユーザー名は `genymotion`、パスワードは EC2 インスタンス ID です。
[詳細](https://docs.genymotion.com/paas/02_Getting_Started/021_AWS/)

4. Configuration を開き、ADB を有効にする

![Enable ADB in Genymotion Cloud](https://user-images.githubusercontent.com/4301109/81505975-46a84e00-92c1-11ea-82a5-8912f96849b1.png)

5. ローカルマシンから、adb で Genymotion Cloud VM に接続できることを確認する。

```bash
adb connect <public_ip>:5555
adb devices
```
![ADB connect](https://user-images.githubusercontent.com/4301109/81506018-9be45f80-92c1-11ea-8486-fcac8daee7be.png)

6. MobSF の Docker イメージ起動時に、環境変数 `MOBSF_ANALYZER_IDENTIFIER` を `<public_ip>:5555` に設定する（例: `3.81.202.69:5555`）。

7. これで AWS 上の Genymotion Cloud VM を使って MobSF Dynamic Analysis を実行できます。

## Corellium Android VM

?> 対応: **rooted userdebug** ビルド、arm64、Android **7.1.2 - 11.0**（最大 **API 30**）

!> 非 rooted の **user** ビルドを選んではいけません。MobSF には rooted の **userdebug** ビルドが必要です。

1. 対応する rooted **userdebug** Android デバイスを作成したら、Corellium の `Connect via VPN` 手順に従う。

![Corellium Android](https://github.com/MobSF/Mobile-Security-Framework-MobSF/assets/4301109/f384421c-98af-47b1-8d98-29641d9ca974)

2. 提供された VPN 設定を使って Corellium ネットワークに接続する。

3. ホストから `adb connect` を実行し、接続できることを確認する。

![Corellium adb](https://github.com/MobSF/Mobile-Security-Framework-MobSF/assets/4301109/c6f1135e-b1ef-4a14-b9bf-6ebfab2e3cca)

4. MobSF の Docker イメージ起動時に、環境変数 `MOBSF_ANALYZER_IDENTIFIER` を Corellium Android デバイスの `<private_ip>:<port>` に設定する（例: `10.11.1.1:5001`）。


## Corellium iOS VM

MobSF v4.4.0 以降では、Corellium iOS VM に Frida Server >17.0.0 がインストールされている必要があります。

!> jailbreak されていないデバイスは MobSF では使えません。

1. Corellium アカウントを用意したら、https://app.corellium.com/profile/api から API key を作成する。

![Corellium API](https://user-images.githubusercontent.com/4301109/289017703-b6f25054-d1b5-4c0e-a781-68b18260fb6a.png)

2. API key を環境変数 `MOBSF_CORELLIUM_API_KEY` に設定する。別ドメインの Corellium エンタープライズ版を使う場合は、環境変数 `MOBSF_CORELLIUM_API_DOMAIN` に正しいドメインも設定してください。

3. MobSF の HTTPS プロキシを有効にするには、iOS VM 側でプロキシを設定します。iPhone の `Settings` → `Wi-Fi` → `Corellium` Wi‑Fi を選択 → 下へスクロールして `Configure Proxy` → `Manual configuration` を選択 → `Server` に `127.0.0.1`、`Port` に `1337` を設定 → `Save`。

![iOS HTTPS Proxy](https://user-images.githubusercontent.com/4301109/289017713-ffc54f0e-1c23-484d-a612-0318ad41e7a3.png)

4. MobSF を起動すると、Corellium 上で jailbroken iOS VM を作成・管理して Dynamic Analysis を行えます。

## Jailbroken iOS device (Beta)

!> この機能は **early beta** です。これまでに **iPhone 6s** および **iPhone 8** で、**checkra1n** と **palera1n** の jailbreak を使って動作確認されています。不具合らしき挙動があれば **Slack** チャンネルまでご連絡ください。

MobSF は v4.5.1 以降、**物理の jailbreak 済み iOS デバイス**に対応しています。接続は **Wi‑Fi**（デバイス IP）または **USB**（例: ポート転送により、コンテナから `127.0.0.1` や `host.docker.internal` に到達）が可能です。

0. iPhone を **USB** で接続するホストに **libimobiledevice** をインストールし、`iproxy` および `idevice*` ツールを使えるようにする（USB ポート転送とデバイス確認用）。**macOS:** `brew install libimobiledevice`。**Debian/Ubuntu:** `sudo apt install libimobiledevice-utils`。
1. OpenSSH（または jailbreak 側の SSH）に到達でき、下記で設定する認証情報と一致することを確認する。
2. **`MOBSF_IOS_ANALYZER_IDENTIFIERS`** を SSH ソケット（`IP:PORT`）に設定する。例: `192.168.1.100:22`、複数デバイス: `192.168.1.100:22,192.168.1.101:22`。
3. デフォルト（`root` / `alpine`）と異なる場合は、任意で **`MOBSF_IOS_SSH_USER`** と **`MOBSF_IOS_SSH_PASSWORD`** を設定する。
4. できれば MobSF ホストと iPhone を同じ Wi‑Fi / LAN に接続する。MobSF の HTTPS プロキシを有効にするには、iOS デバイス側でプロキシを設定します。iPhone の `Settings` → `Wi-Fi` → Wi‑Fi SSID を選択 → 下へスクロールして `Configure Proxy` → `Manual configuration` を選択 → `Server` に `<ip address of mobsf host>`、`Port` に `1337` を設定 → `Save`。
