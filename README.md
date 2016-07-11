redmine-docker
============================================================

要求条件
------------------------------------------------------------
- docker
- docker-compose

設定
------------------------------------------------------------
redmineのdocker official imgをベースに下記を追加を設定する

- app
  - OS追加設定
    - fonts-ipafont-gothic
  - redmine追加設定
    - 日本語設定パス
      - config/configuration.yml: rmagick_font_path: /usr/share/fonts/opentype/ipafont-gothic/ipag.ttf
    - redmine_theme
      - farend_basic
    - redmine plugin
      - [redmine_issue_templates](https://github.com/akiko-pusu/redmine_issue_templates)
      - [clipboard_image_paste](https://github.com/peclik/clipboard_image_paste)
      - [redmine_github_hook](https://github.com/koppen/redmine_github_hook)
      - [redmine_code_review](http://www.r-labs.org/projects/r-labs/wiki/Code_Review)
      - [sidebar_hide](https://github.com/bdemirkir/sidebar_hide)
      - [redmine_banner](https://github.com/akiko-pusu/redmine_banner)
      - [redmine_incr_code_search](https://github.com/suer/redmine_incr_code_search)
      - [redmine_emoji](https://github.com/suer/redmine_emoji)

- db
  - PostgreSQL追加設定
    initdb設定: --encoding=UTF-8 --no-locale

起動方法
------------------------------------------------------------

1. ディレクトリに移動する

  ``` shell
  cd redmine-docker
  ```

2. 起動する

  ``` shell
  docker-compose up -d
  ```

3. 起動状態を確認する

  ``` shell
  docker-compose ps
  ```

初期設定
------------------------------------------------------------

1. TOPページにアクセスする

  ``` shell
  http://xxx.xxx.xxx.xxx:xxx/
  ```

2. ログインする
   右上の「ログイン」を押下し、「admin/admin」を入力し「ログイン」を押下する

3. 書籍設定をロードする
   左上の「管理」を押下し、言語を「Japanese(日本語)」を選択し、「デフォルト設定をロード」を押下する

4. adminパスワードを変更する
   左上の「管理」を押下し、「ユーザー」を押下する。
   「admin」を押下して、「パスワード」と、「パスワードの確認」に新しいパスワードを押下して「保存」を押下する
   ログアウトされるため、再度「2」の手順で新しいパスワードを使用してログインする

5. 全般設定を変更する
   左上の「管理」を押下し、「設定」->「全般」タブを押下する。
   下記のように変更し「保存」を押下する

    |設定                              |設定値          |
    |:---------------------------------|:---------------|
    |テキストの書式                      |Markdown    |

6. 表示設定を変更する
   左上の「管理」を押下し、「設定」->「表示」タブを押下する。
   下記のように変更し「保存」を押下する

    |設定                              |設定値          |
    |:---------------------------------|:---------------|
    |テーマ                            |Farend basic    |
    |デフォルトの言語                  |Japanese(日本語)|
    |添付ファイルのサムネイル画像を表示|(check)         |

7. 認証設定を変更する
   左上の「管理」を押下し、「設定」->「認証」タブを押下する。
   下記のように変更し「保存」を押下する

    |設定                            |設定値   |
    |:-------------------------------|:--------|
    |認証が必要                      |(check)  |
    |ユーザによるアカウント削除を許可|(uncheck)|
    |パスワードの再発行              |(uncheck)|

8. プロジェクト設定を変更する
   左上の「管理」を押下し、「設定」->「プロジェクト」タブを押下する。
   下記のように変更し「保存」を押下する

    |設定                                                |詳細設定項目        |設定値   |
    |:---------------------------------------------------|:-------------------|:--------|
    |デフォルトで新しいプロジェクトは公開にする          |                    |(uncheck)|
    |プロジェクトにおいてデフォルトで有効となるモジュール|文書                |(uncheck)|
    |                                                    |ファイル            |(uncheck)|
    |                                                    |バナー              |(check)  |
    |                                                    |コードレビュー      |(check)  |
    |                                                    |コード検索          |(check)  |
    |                                                    |チケットテンプレート|(check)  |
    |プロジェクトにおいてデフォルトで有効となるトラッカー|バグ                |(check)  |
    |                                                    |機能                |(check)  |
    |                                                    |サポート            |(check)  |

9. チケットトラッキング設定を変更する
   左上の「管理」を押下し、「設定」->「チケットトラッキング」タブを押下する。
   下記のように変更し「保存」を押下する

    |設定                        |詳細設定項目|設定値                    |
    |:---------------------------|:-----------|:-------------------------|
    |チケットの一覧で表示する項目|期日        |(select)(更新日の上に移動)|
    |                            |プライベート|(select)(一番に下)        |
    |                            |予定工数    |(check)                   |
    |                            |作業時間    |(check)                   |

10. リポジトリ設定を変更する
   左上の「管理」を押下し、「設定」->「リポジトリ」タブを押下する。
   下記のように変更し「保存」を押下する

    |設定                                     |詳細設定項目  |設定値                    |
    |:----------------------------------------|:-------------|:-------------------------|
    |コミットメッセージ内でチケットの参照/修正|参照キーワード|,*(最後に追記)            |

gitbucket連携
------------------------------------------------------------
本設定はgitbucketのリポジトリを連携する毎に実施する。
cloneは、redmine dockerのホストで実施すること

1. 連携するリポジトリをホストOS上でredmineのvolumesの下にrootでbase cloneする
   ```shell
   cd /opt/docker/redmine/volumes/app/opt/redmine/repos
   git clone --bare <連携するリポジトリのhttp://〜.gitまでのURL>
   chmod -R 777 <cloneしたリポジトリ>(.gitも入力)
   ```

2. プロジェクトとリポジトリを連携させる
   リポジトリ設定が可能なアカウントでredmineにログインする
   左上の「プロジェクト」を押下し、設定対象のプロジェクトを選択する
   「設定」タブ->「リポジトリ」->「新しいリポジトリ」を押下する
   下記のように変更し「保存」を押下する

    |設定                                          |設定値                                              |
    |:---------------------------------------------|:---------------------------------------------------|
    |バージョン管理システム                        |Git                                                 |
    |識別子                                        |(任意)                                              |
    |リポジトリのパス                              |/opt/redmine/repos/<cloneしたリポジトリ>(.gitも入力)|
    |ファイルとディレクトリの最新コミットを表示する|(check)                                             |

3. gitbucketにWebHookを設定する
  1. gitbucketのリポジトリで、「Settings」を押下し、「Service Hooks」タブを選択する

  2. 「Add webhook」を押下する。

  3. 下記を設定し、「Add webhook」を押下する

      |設定       |設定値                                                                                   |
      |:----------|:----------------------------------------------------------------------------------------|
      |Payload URL|http://<Redmineのサイト>/github_hook?project_id=<プロジェクトのID>&repository_id=<識別子>|
