# よくある質問（FAQ）

## 認証を無効にしたい。認証なしで MobSF を動かしたい。

### 解決方法

環境変数 `MOBSF_DISABLE_AUTHENTICATION=1` を設定すると、MobSF の Authentication と Authorization を無効にできます。REST API は常に API Key が必要で、こちらは無効化できません。ただし環境変数 `MOBSF_API_KEY` で独自の API Key を設定できます。

## Static Analysis 実行時のデータベース関連例外

```
[ERROR] Saving to DB (E:\Mobile-Security-Framework-MobSF\StaticAnalyzer\views\android\db_interaction.py,
 LINE 236 "static_db.save()"): table StaticAnalyzer_staticanalyzerandroid has no column named 
```

### 解決方法

上記のような例外が出る場合、多くの場合はデータベーススキーマが変わった新しいバージョンの MobSF を使っていることが原因です。デプロイ方式に応じて、データベースマイグレーションの手順に従ってください。

* [Updating MobSF（ホストインストール向け）](/ja/develop#updating-mobsf)
* [Updating MobSF（volume mount した Docker コンテナ向け）](/ja/docker_options#updating-mobsf-version-when-volume-mount-is-used)

## Dynamic Analysis: APK のインストールに失敗する


```
[INFO] 24/Sep/2020 13:50:56 - Installing APK
adb: failed to install C:/Users/XXXX/Mobile-Security-Framework-MobSF/uploads/xxxx/xxxx.apk: Failure [INSTALL_FAILED_NO_MATCHING_ABIS: Failed to extract native libraries, res=-113]
[ERROR] 24/Sep/2020 13:50:59 - This APK cannot be installed. Is this APK compatible the Android VM/Emulator?
```

```
[INFO] 24/Sep/2020 13:50:56 - Installing APK
adb: failed to install /x/xxx/xxx.apk: Failure [INSTALL_FAILED_MISSING_SHARED_LIBRARY: Package couldn't be installed in /data/app/com.xxx.xxx-1: Package com.xxx.xxxx requires unavailable shared library com.google.android.maps; failing!]
```

### 解決方法
`INSTALL_FAILED_NO_MATCHING_ABIS` エラーは、ARM などサポートされていないアーキテクチャ向けのネイティブライブラリを含むアプリをインストールしようとしたときに表示されます。
Genymotion や Android Studio Emulator の x86 は、Android の x86 / x86_64 アーキテクチャで動作するため、ARM ライブラリは動きません。適切なプラットフォーム向けの APK を用意するか、Dynamic Analysis 用に ARM アーキテクチャの Android Studio Emulator を使ってください。


Genymotion では、ARM translation モジュールを使うと一部の ARM 向けアプリを動かせる場合があります。参照: [Genymotion ARM Translation](https://github.com/m9rco/Genymotion_ARM_Translation)
アプリの実行に Play Store サービスなどの Google Apps が必要な場合もあります。その場合は、Dynamic Analysis に使っている Android バージョンに合った GApps をインストールしてください。参照: [Open GApps](https://opengapps.org/)


## Dynamic Analysis: Linux ホスト上の MobSF Docker が、ホスト上の AVD / Genymotion と通信できない

Ubuntu やその他の Linux ベースのホストで AVD または Genymotion エミュレータを起動した状態で Dynamic Analysis を行い、次のように Docker イメージを実行したあと、
    
```bash
docker run -it --rm \
    -e MOBSF_ANALYZER_IDENTIFIER=127.0.0.1:6555 \
    -p 8000:8000 \
    -p 1337:1337 \
    opensecurity/mobile-security-framework-mobsf:latest
```
`failed to connect to 'host.docker.internal:6555': Connection refused` のような接続エラーが出る場合。

### 解決方法
`MOBSF_ANALYZER_IDENTIFIER` に localhost の IP が入っています。Docker イメージが localhost 上の AVD / Genymotion と正しく通信するには、`host.docker.internal` を使ってホスト OS へトラフィックをルーティングする必要があります。Linux 系 OS では、これはそのままでは動きません。まず Docker のバージョンが `>=20.10.0` であることを確認し、MobSF の Docker イメージ起動時に追加フラグ `--add-host=host.docker.internal:host-gateway` を付けてください。
これで、MobSF コンテナ内の adb と、localhost IP で動いている Linux ホスト上の AVD / Genymotion エミュレータ間のネットワーク接続が直るはずです。

例: 
```bash
docker run -it --rm \
    -e MOBSF_ANALYZER_IDENTIFIER=127.0.0.1:6555 \
    -p 8000:8000 \
    -p 1337:1337 \
    --add-host=host.docker.internal:host-gateway \
    opensecurity/mobile-security-framework-mobsf:latest
```

上記は Android Studio の AVD ではうまくいかない場合があります。AVD の場合は `scripts/start_avd.sh` を実行し、正しい `MOBSF_ANALYZER_IDENTIFIER` を取得してください。

例:
```bash
scripts/start_avd.sh Pixel_5_API_30
...
...
socat listener started on port 5556 forwarding to 5555 in the host.
Docker users please set the environment variable MOBSF_ANALYZER_IDENTIFIER=host.docker.internal:5556 for adb connectivity.

docker run -it --rm \
    -p 8000:8000 \
    -p 1337:1337 \
    --add-host=host.docker.internal:host-gateway \
    -e MOBSF_ANALYZER_IDENTIFIER=host.docker.internal:5556 \
    opensecurity/mobile-security-framework-mobsf:latest
```

それでも動かない場合は、Docker コンテナを host ネットワークで起動してみてください。

例: 
```bash
docker run -it --rm \
    --net=host \
    -e MOBSF_PLATFORM=linux \
    -e MOBSF_ANALYZER_IDENTIFIER=127.0.0.1:6555 \
    -p 8000:8000 \
    -p 1337:1337 \
    opensecurity/mobile-security-framework-mobsf:latest
```
