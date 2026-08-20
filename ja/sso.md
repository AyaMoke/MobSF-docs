## MobSF の SSO 対応

MobSF はバージョン 4.0.1 以降、SAML2 による SSO に対応しています。

ユーザーロールは次の 3 つです。

1. `Admin`: 初期セットアップ時に作成されるプライマリ管理者アカウントです。他ロールのユーザーを管理できます。
2. `Maintainer`: スキャン、suppress、削除の権限があります。
3. `Viewer`: 読み取り専用で、スキャン結果の閲覧のみ可能です。

SSO 連携では、`Maintainer` と `Viewer` ロールのみをサポートします。


!> SSO 有効時は、パスワード認証と `Admin` ロールがデフォルトで無効になります。パスワードログインや admin アカウントを許可するには、MobSF 起動前に環境変数 `MOBSF_SP_ALLOW_PASSWORD` を `1` に設定してください。


### Okta SSO のセットアップ方法

このセクションでは、MobSF と Okta で SSO を設定する手順を説明します。

Okta SSO を設定するには、MobSF の Assertion Consumer Service URL が必要です。

* **Assertion Consumer Service (ACS) URL** — Okta が SAML assertion を HTTP POST で送信する先です。ACS URL は `<http/https>://<mobsf_host>:<mobsf_port>/sso/acs/` です。たとえばローカル環境で MobSF を動かしている場合、ACS URL は `http://localhost:8000/sso/acs/` になります。

MobSF で Okta SSO を有効にするには、Okta の Metadata URL が必要です。

* **Metadata URL** — entity ID、X509 Certificate、SSO URL など、MobSF が必要とするメタデータ情報を含む Okta の URL です。

#### Okta Configuration

1. Okta Admin アカウントにログインする。
2. **Applications** で **Create App Integration** をクリックする。
3. **SAML 2.0** を選び **Next** をクリックする。
4. **General Settings** で次を設定する:
    * **App Name**: `MobSF`
    * **App logo**: [MobSF ロゴを使用](https://github.com/MobSF/Mobile-Security-Framework-MobSF/assets/4301109/9925f41c-bd53-4ef5-a4e5-e6b18ce4ba6d)

<img width="1060" alt="MobSF App Integration" src="https://github.com/MobSF/Mobile-Security-Framework-MobSF/assets/4301109/fefcbe0f-3c4b-400d-9e88-dfd19e836e73">

5. 次の **Configure SAML** タブで次を設定する:
    * **Single sign-on URL**: `<MobSF ACS URL>`
        * `Use this for Recipient URL and Destination URL` にチェックを入れる
    * **Audience URI (SP Entity ID)**: `<MobSF ACS URL>`
    * **Name ID format**: `EmailAddress`
    * **Application username**: `Email`
    * **Update application username on**: `Create and Update`
6. **Attribute Statements (optional)** セクションで、ユーザーのメールを MobSF に送る属性ステートメントを新規作成する。
    * **Name**: `email`
    * **Name format**: `Unspecified`
    * **Value**: `user.email`
7. **Group Attribute Statements (optional)** セクションで、適切なロールを MobSF に送る属性ステートメントを新規作成する。
    * **Name**: `role`
    * **Name format**: `Unspecified`
    * **Filter**: `Matches Regex`、`.*`

<img width="559" alt="SAML settings" src="https://github.com/MobSF/Mobile-Security-Framework-MobSF/assets/4301109/b7b9a2de-8d11-4f24-b8ac-1efe893ebafd">

8. 次の **Feedback** 画面で `This is an internal app that we have created` にチェックを入れ、**Finish** をクリックして MobSF Okta 連携を作成する。

9. MobSF ロール `Maintainer` と `Viewer` 用に、少なくとも 2 つの Okta グループを作成してください。グループ名に文字列 `maintainer` が含まれていると `Maintainer` ロールに関連付けられ、それ以外のグループ名は読み取り専用の `Viewer` ロールに割り当てられます。各グループに対応するユーザーを追加できます。

10. MobSF アプリの **Assignment** タブを開き、`Maintainer` と `Viewer` ロールに対応するグループを割り当てる。

<img width="566" alt="Screenshot 2024-05-22 at 8 36 41 PM" src="https://github.com/MobSF/Mobile-Security-Framework-MobSF/assets/4301109/f419add2-66a5-47b7-93da-349a5d958cd2">


11. 作成したアプリの **Sign On** タブを開き、`Metadata URL` をコピーする。
<img width="556" alt="Metadata URL" src="https://github.com/MobSF/Mobile-Security-Framework-MobSF/assets/4301109/9e193176-1c26-48ba-ad92-54950b050344">



#### MobSF Configuration

1. Okta の MobSF アプリ連携を作成したら、環境変数 `MOBSF_IDP_METADATA_URL` に Okta の `Metadata URL` を設定する。

2. MobSF を起動すると、Okta による SSO が有効になります。


### SSO FAQ

#### リバースプロキシ配下の MobSF

Nginx などのリバースプロキシ配下で MobSF を動かす場合は、`X-Forwarded-Host`、`X-Forwarded-Port`、`X-Forwarded-For` などのヘッダーを設定し、実際のドメイン名が MobSF に届くようにしてください。

**Nginx 設定例**

```
location / {
    proxy_set_header        X-Forwarded-Host    $host;
    proxy_set_header        X-Forwarded-Port    443;
    proxy_set_header        X-Forwarded-For     $proxy_add_x_forwarded_for;
    ....
}
```

あるいは、環境変数 `MOBSF_SP_HOST` でホスト名を直接設定することもできます。例: `MOBSF_SP_HOST=https://mobsf.yourdomain.com`


`Invalid dict settings: sp_acs_url_invalid` のようなエラーは、MobSF が正しいホスト名を見つけられなかったことを示します。
