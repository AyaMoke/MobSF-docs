# MobSFの起動

?> MobSF を実行するには [Docker](https://docs.docker.com/get-docker/) が必要です。快適に利用するにはバージョン `20.10.0` 以上を推奨します。

最新の機能・セキュリティ修正・バグ修正を確実に得るには、`latest` タグの最新 MobSF Docker イメージを取得してください。バージョン付きの [releases](https://hub.docker.com/r/opensecurity/mobile-security-framework-mobsf/tags) も提供していますが、最新の更新が含まれない場合があります。

## Static Analysis（静的解析）

### 静的解析対応で MobSF を起動する

最新の Docker イメージを取得し、MobSF コンテナを起動します。

```
docker pull opensecurity/mobile-security-framework-mobsf:latest
docker run -it --rm \
    -p 8000:8000 \
    opensecurity/mobile-security-framework-mobsf:latest
```

ブラウザで `http://127.0.0.1:8000` を開くと、MobSF の Web インターフェースにアクセスできます。デフォルトのログイン情報は `mobsf/mobsf` です。

## Dynamic Analysis（動的解析）
### 静的解析と動的解析の両方に対応して起動する

MobSF を起動する前に、**[対応](/ja/dynamic_analyzer_docker)** している Android VM / エミュレータのいずれかを起動し、`MOBSF_ANALYZER_IDENTIFIER` を取得してください。iOS の場合は Corellium、または jailbreak 済みの iOS デバイスを使用します。

```
docker run -it --rm \
    -p 8000:8000 \
    -p 1337:1337 \
    -e MOBSF_ANALYZER_IDENTIFIER=<adb device identifier> \
    opensecurity/mobile-security-framework-mobsf:latest
```

`<adb device identifier>` の正しい値の取得方法は [こちら](/ja/dynamic_analyzer_docker) を参照してください。

!> Ubuntu など Linux 系 OS では、Docker のバージョンが `20.10.0` 以上であることを確認してください。MobSF Docker コンテナ起動時には、追加オプション `--add-host=host.docker.internal:host-gateway` を付けてください。この設定がないと、ホストマシンの `localhost` 上で動いている Android VM / エミュレータと MobSF コンテナが通信できません。次の手順に従い、エミュレータへのトラフィック転送が必要になる場合もあります。

```
# Install socat
sudo apt install socat

# Run start_avd script
scripts/start_avd.sh <avd-name>
# The script will output the MOBSF_ANALYZER_IDENTIFIER
```
```
# Example usage:
$ scripts/start_avd.sh Pixel_5_API_30
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


?> MobSF の Dynamic Analysis は、**rooted** な Android バージョン `4.1` から `11.0`（最大 `API 30`）のみ対応しています。Android `12` 以降はサポートされていません。


### iOS の Dynamic Analysis 対応で起動する

**Option A — Corellium iOS VM**

MobSF を起動する前に、Corellium の [API key](https://app.corellium.com/login) を取得してください。

```
docker run -it --rm \
    -p 8000:8000 \
    -p 1337:1337 \
    -e MOBSF_CORELLIUM_API_KEY=<corellium api key> \
    opensecurity/mobile-security-framework-mobsf:latest
```

**Option B — Jailbroken iOS Device (Beta)**

MobSF と jailbreak 済み iOS デバイスを同じ Wi‑Fi / LAN に接続してください。デバイスの Wi‑Fi IP と SSH ポートを使用します。

```
docker run -it --rm \
    -p 8000:8000 \
    -p 1337:1337 \
    -e MOBSF_IOS_ANALYZER_IDENTIFIERS=192.168.1.100:22 \
    opensecurity/mobile-security-framework-mobsf:latest
```

詳細とプロキシ設定: [Dynamic Analyzer — Jailbroken iOS device](/ja/dynamic_analyzer_docker#jailbroken-ios-device-beta)。

## MobSF チュートリアル

Defcon Demo Labs 2020 のこの動画では、MobSF の基本機能が解説されています。

<iframe width="760" height="515" src="https://www.youtube.com/embed/1NIQs82n3nw" title="MobSF Defcon Demo Labs Talk" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

MobSF と ZAP によるモバイルアプリの Live HTTPS トラフィック傍受。

<iframe width="760" height="515" src="https://www.youtube.com/embed/sGTrPRJ31Uw" title="Live HTTPs Traffic Interception with MobSF and ZAP" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

MobSF、Frida、ChatGPT で Android CTF を解く。

<iframe width="760" height="515" src="https://www.youtube.com/embed/K-2n82MZtjU" title="Solving an Android CTF with MobSF, Frida and ChatGPT" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

MobSF をこれから始める場合は、[Automated Mobile Application Security Assessment with MobSF](https://opsecx.com/index.php/product/automated-mobile-application-security-assessment-with-mobsf/) コースが参考になります。MobSF の主要機能を一通り学べる初心者向けの内容です。

### その他のリソース

* [Past MobSF Presentations & Slides](https://mobsf.github.io/Mobile-Security-Framework-MobSF/presentations.html)
* [Community Generated Playlist](https://youtube.com/playlist?list=PLX3EwmWe0cS9SRHpuuiRA-CsxevX3hh6o&si=5o3Mt6a6q9lmvuDn)
