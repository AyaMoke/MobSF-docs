# Extra Features（追加機能）

## REST API

MobSF は REST API を提供しています。最新の API ドキュメントは、アプリ内の `/api_docs` ルートで確認できます。
***
## CI/CD

CI/CD では MobSF REST API を活用できます。

**MobSF CI/CD パイプライン連携**

* **MobSF - Bitrise** - [iOS Security Analysis with MobSF](https://www.netguru.com/codestories/ios-security-analysis-with-mobsf) | [Run your security static analysis tests on the cloud with MobSF, AWS, and Bitrise](https://bitrise.io/blog/post/run-your-security-static-analysis-tests-on-the-cloud-with-mobsf-aws-and-bitrise)
* **MobSF - OWASP Glue** - [How To: (Continuously) Hacking Your App](https://medium.com/@omerlh/how-to-continuously-hacking-your-app-c8b32d1633ad)
* **MobSF - Jenkins** - [Achieving DevSecOps: Mobile App Security Integration Using Jenkins and MobSF](https://medium.com/@debasishkumardas5/achieving-devsecops-mobile-app-security-integration-using-jenkins-and-mobsf-187568f74d4c)
* **MobSF - Gitlab CI** [Running MobSF SAST using Gitlab CI Service](https://waristea.medium.com/running-mobsf-sast-using-gitlab-ci-service-7c3ac3a48648) | [GitLab CI template for MobSF](https://to-be-continuous.gitlab.io/doc/ref/mobsf/)
* **mobsfscan SAST CI/CD** - [mobsfscan](https://github.com/MobSF/mobsfscan)
***


## Mass Static Analysis（一括静的解析）

* MobSF サーバーを起動する。
`./run.sh` または `run.bat`
* コンソールから **REST API key** を取得する。
* [mass_static_analysis.py](https://github.com/MobSF/Mobile-Security-Framework-MobSF/blob/master/scripts/mass_static_analysis.py) を実行する。

```bash
pip install requests
python mass_static_analysis.py
usage: mass_static_analysis.py [-h] [-d DIRECTORY] [-s IPPORT] [-k APIKEY]
                               [-r RESCAN]

optional arguments:
  -h, --help            show this help message and exit
  -d DIRECTORY, --directory DIRECTORY
                        Path to the directory that contains mobile app
                        binary/zipped source code
  -s IPPORT, --ipport IPPORT
                        IP address and Port number of a running MobSF Server.
                        (ex: 127.0.0.1:8000)
  -k APIKEY, --apikey APIKEY
                        MobSF REST API Key
  -r RESCAN, --rescan RESCAN
                        Run a fresh scan. Value can be 1 or 0 (Default: 0)
```

例: 
```bash
python mass_static_analysis.py -s 127.0.0.1:8000  -k <rest_api_key> -d /home/files/
```
***
## VirusTotal Scan

VirusTotal Scan はデフォルトで無効です。有効にする前に VirusTotal API Key を設定してください。

* VirusTotal API Key の取得は [こちら](https://www.virustotal.com/#/join-us)
* API Key は https://www.virustotal.com/en/user/[username]/apikey/ から確認できます。
* MobSF 起動前に環境変数 `MOBSF_VT_ENABLED=1` と `MOBSF_VT_API_KEY=<your-api-key>` を設定する。
* VirusTotal へのバイナリアップロードを有効にするには、環境変数 `MOBSF_VT_UPLOAD=1` を設定します。このオプションはデフォルトで無効です。
***
## AppMonsta Play Store Info

Google Play Store から詳細を取得するフォールバックとして AppMonsta API を使用します。デフォルトでは無効です。有効にするには AppMonsta API Key が必要です。

* AppMonsta API Key の取得: [AppMonsta API Key](https://appmonsta.com/dashboard/get_api_key/)
* MobSF 起動前に環境変数 `MOBSF_APPMONSTA_API=<appmonsta-api-key>` を設定する。
***
## SQLite の代わりに Postgres DB を使う

デフォルトでは MobSF はデータベースに SQLite を使用します。必要に応じて PostgreSQL バックエンドに切り替えられます。

PostgreSQL を構成するには、MobSF 起動前に次の環境変数を設定してください。

    POSTGRES_USER=postgres
    POSTGRES_PASSWORD=password
    POSTGRES_DB=mobsf
    POSTGRES_HOST=postgres
    POSTGRES_PORT=5432

**マイグレーションの適用**

```bash
poetry run python manage.py makemigrations 
poetry run python manage.py makemigrations StaticAnalyzer
poetry run python manage.py migrate
poetry run python manage.py create_roles
```

その後 MobSF サーバーを再起動すると、データベースとして PostgreSQL が構成されます。

Postgres と Nginx を使う MobSF の docker compose テンプレートは [こちら](docker_options.md?id=for-postgres-and-nginx-reverse-proxy-support) も参照してください。
