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
      - [redmine_mattermost](https://github.com/altsol/redmine_mattermost)
      - [redmine-view-customize](https://github.com/onozaty/redmine-view-customize)

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

  - 2回目以降
    ``` shell
    docker-compose start
    ```

  - 初回
    ``` shell
    docker-compose up -d
    ```

3. 起動状態を確認する

  ``` shell
  docker-compose ps
  ```

停止方法
------------------------------------------------------------

1. ディレクトリに移動する

  ``` shell
  cd redmine-docker
  ```

2. 起動状態を確認する

  ``` shell
  docker-compose ps
  ```

3. 停止する

  ``` shell
  docker-compose stop
  ```

4. 停止状態を確認する

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

3. 初期設定をロードする

   左上の「管理」を押下し、言語を「Japanese(日本語)」を選択し、「デフォルト設定をロード」を押下する

4. adminパスワードを変更する

   左上の「管理」を押下し、「ユーザー」を押下する。
   「admin」を押下して、「パスワード」と、「パスワードの確認」に新しいパスワードを押下して「保存」を押下する
   ログアウトされるため、再度「2」の手順で新しいパスワードを使用してログインする

5. トラッカーを追加する

   左上の「管理」を押下し、「トラッカー」を押下する。
   「新しいトラッカーを作成」を押下して、下記のように変更し「保存」を押下する。
   一覧画面で「ソート」を仕様して一番上に来るようにする
   (繰り返し実施する)

    |設定                        |設定値1 |設定値2  |
    |:---------------------------|:-------|:--------|
    |名称                        |タスク  |調査     |
    |デフォルトのステータス      |新規    |新規     |
    |標準フィールド              |予定工数|(uncheck)|
    |ワークフローをここからコピー|バグ    |バグ     |

6. チケットのステータスに進捗率を設定する

   左上の「管理」を押下し、「チケットのステータス」を押下する。
   各ステータスを押下して、下記のように変更し「保存」を押下する。

    |ステータス    |進捗率|
    |:-------------|:-----|
    |新規          |0%    |
    |進行中        |10%   |
    |解決          |80%   |
    |フィードバック|60%   |
    |終了          |100%  |
    |却下          |100%  |

7. グローバルチケットテンプレートを登録する

   左上の「管理」を押下し、「グローバルチケットテンプレート」を押下する。
   「新規作成」を押下して、下記のように変更し「保存」を押下する。

    |設定            |設定値1           |設定値2         |
    |:---------------|:-----------------|:---------------|
    |テンプレート名  |タスクテンプレート|調査テンプレート|
    |トラッカー      |タスク            |調査            |
    |チケットタイトル|タスクチケット概要|調査チケット概要|
    |チケット本文    |(欄外に記載)      |(欄外に記載)    |
    |有効            |(check)           |(check)         |

    - タスクテンプレートチケット本文
    ```
目的
============================================================
- このタスクを実施する目的

内容
============================================================
- 具体的に実施してほしい内容を書く
    ```

    - 調査テンプレートチケット本文
    ```
目的
============================================================
- 調査している内容に期待している結果

現象
============================================================
- (あれば)調査する現象について記述

手順
============================================================
- (あれば)再現手順を記述

期待する効果
============================================================
- 実際にはどうなるべきなのかを記述

環境
============================================================
- 調査環境について記述

例
- OS: Mac OS X 10.10
- ブラウザ: IE11
- 実行ユーザー: admin
- 発生URL: https://xxx.xxx.xx.xxx/example

関連情報
============================================================
- エラーログ、メッセージなどを記述
    ```

8. View customizeを登録する

   左上の「管理」を押下し、「View customize」を押下する。
   「New view cutomize」を押下して、下記のように変更し「保存」を押下する。

    - チケット作成時(コピー時含む)自動でチケット担当者を作成者とし、期日を7日後に設定
      - Path pattern: /issues/new$|/issues/[0-9]+/copy$
      - Type: JavaScript
      - Code:
      ```javascript
      $(function() {
        today = new Date();
        today.setDate(today.getDate() + 7);

        month = today.getMonth() + 1;
        if ( month < 10 ) {
          month = '0' + month;
        }
        day = today.getDate();
        if ( day < 10 ) {
          day = '0' + day;
        }

        var setDefalutValue = function() {
          $('#issue_assigned_to_id option:contains("自分")').attr('selected', true);
          $('#issue_due_date').val(today.getFullYear() + '-' + month + '-' + day);
        }
        setDefalutValue();
      });
      ```

    - ステータス、担当者、期日欄の背景を黄色にする
      - Path pattern: /issues/*
      - Type: StyleSheet
      - Code:
      ```css
      #issue_status_id {
        background-color: #ffffcc;
      }

      #issue_assigned_to_id {
        background-color: #ffffcc;
      }

      #issue_due_date {
        background-color: #ffffcc;
      }
      ```

9. PJ管理者を追加する

   左上の「管理」を押下し、「ロールと権限」を押下する。
   「管理者」の「コピー」を押下して、下記のように変更し「保存」を押下する。

    |設定        |詳細設定項目      |設定値                                |詳細設定値   |
    |:-----------|:-----------------|:-------------------------------------|:------------|
    |新しいロール|名称              |PJ管理者                              |             |
    |            |表示できるチケット|プライベートチケット以外              |             |
    |            |表示できるユーザー|見ることができるプロジェクトのメンバー|             |
    |            |メンバーの管理    |次のロールのみ                        |(開発者check)|
    |            |                  |                                      |(報告者check)|
    |権限        |プロジェクト      |プロジェクトの追加                    |(uncheck)    |
    |            |                  |プロジェクトの編集                    |(uncheck)    |
    |            |                  |プロジェクトの終了/再開               |(uncheck)    |
    |            |                  |モジュールの選択                      |(uncheck)    |

10. 全般設定を変更する

   左上の「管理」を押下し、「設定」->「全般」タブを押下する。
   下記のように変更し「保存」を押下する

    |設定                              |設定値          |
    |:---------------------------------|:---------------|
    |テキストの書式                      |Markdown    |

11. 表示設定を変更する

   左上の「管理」を押下し、「設定」->「表示」タブを押下する。
   下記のように変更し「保存」を押下する

    |設定                              |設定値          |
    |:---------------------------------|:---------------|
    |テーマ                            |Farend basic    |
    |デフォルトの言語                  |Japanese(日本語)|
    |ユーザー名の表示形式              |Admin Redmine   |
    |添付ファイルのサムネイル画像を表示|(check)         |

12. 認証設定を変更する

   左上の「管理」を押下し、「設定」->「認証」タブを押下する。
   下記のように変更し「保存」を押下する

    |設定                            |設定値   |
    |:-------------------------------|:--------|
    |認証が必要                      |(check)  |
    |ユーザによるアカウント削除を許可|(uncheck)|
    |パスワードの再発行              |(uncheck)|

13. プロジェクト設定を変更する

   左上の「管理」を押下し、「設定」->「プロジェクト」タブを押下する。
   下記のように変更し「保存」を押下する

    |設定                                                |詳細設定項目        |設定値   |
    |:---------------------------------------------------|:-------------------|:--------|
    |デフォルトで新しいプロジェクトは公開にする          |                    |(uncheck)|
    |プロジェクトにおいてデフォルトで有効となるモジュール|時間管理            |(uncheck)|
    |                                                    |文書                |(uncheck)|
    |                                                    |ファイル            |(uncheck)|
    |                                                    |フォーラム          |(uncheck)|
    |                                                    |バナー              |(check)  |
    |                                                    |コードレビュー      |(check)  |
    |                                                    |コード検索          |(check)  |
    |                                                    |チケットテンプレート|(check)  |
    |プロジェクトにおいてデフォルトで有効となるトラッカー|タスク              |(check)  |
    |                                                    |調査                |(check)  |

14. チケットトラッキング設定を変更する

   左上の「管理」を押下し、「設定」->「チケットトラッキング」タブを押下する。
   下記のように変更し「保存」を押下する

    |設定                        |詳細設定項目|設定値                    |
    |:---------------------------|:-----------|:-------------------------|
    |進捗率の算出方法            |-           |チケットのステータスに連動|
    |チケットの一覧で表示する項目|期日        |(select)(更新日の上に移動)|
    |                            |プライベート|(select)(一番に下)        |
    |                            |予定工数    |(check)                   |
    |                            |作業時間    |(check)                   |

15. ファイル設定を変更する

   左上の「管理」を押下し、「設定」->「ファイル」タブを押下する。
   下記のように変更し「保存」を押下する

    |設定                                      |設定値            |
    |:-----------------------------------------|:-----------------|
    |添付ファイルとリポジトリのエンコーディング|UTF-8,CP932,EUC-JP|

16. リポジトリ設定を変更する

   左上の「管理」を押下し、「設定」->「リポジトリ」タブを押下する。
   下記のように変更し「保存」を押下する

    |設定                                     |詳細設定項目  |設定値        |
    |:----------------------------------------|:-------------|:-------------|
    |コミットメッセージ内でチケットの参照/修正|参照キーワード|,*(最後に追記)|

gitbucket連携
------------------------------------------------------------
本設定はgitbucketのリポジトリを連携する毎に実施する。
cloneは、redmine dockerのホストで実施すること

1. 連携するリポジトリをホストOS上でredmineのvolumesの下にrootでbase cloneする

   ※ 認証が必要な場合は、**http://\<ユーザ名\>\:\<パスワード\>@\<ホスト名\>/...**の形式で認証情報を渡すこと

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
      |Payload URL|http://\<Redmineのサイト\>/github_hook?project_id=<プロジェクトのID>&repository_id=<識別子>|

mattermost連携
------------------------------------------------------------
本設定はredmineとmattermostを連携させる。

- mattermostでWebHookを有効化する(システム管理者)

  1. 左上のメニューから「システムコンソール」を押下する

  2. 「設定」->「統合機能」->「ウェブフックとコマンド」を押下し、下記を設定する

    |メニュー                                                      |設定値|
    |:-------------------------------------------------------------|:----|
    |外向きのウェブフックを有効化する                              |有効 |
    |外向きのウェブフックを有効化する                              |有効 |
    |ウェブフックまたはスラッシュコマンドでのユーザー名を上書きする|有効 |

- mattermostでredmineからmattermostへのアクセス設定(チーム管理者/システム管理者)

  1. 左上のメニューから「統合機能」を押下する

  2. 「内向きのウェブフック」->「内向きのウェブフックを追加する」を押下する

  3. 下記を設定し、「保存する」を押下する

    |メニュー    |設定値                           |
    |:-----------|:--------------------------------|
    |Display Name|(任意)                           |
    |説明        |(任意)                           |
    |チャネル    |(botを動作させたいチャネルを指定)|

- redmineでmattermostの設定を行う。

  1. redmine pluginの設定を行う

     左上の「管理」を押下し、「プラグイン」を押下する。
     「Redmine Mattermost」の「設定」を押下して、下記のように変更し「適用」を押下する。

      |設定               |設定値                             |
      |:------------------|:----------------------------------|
      |Mattermost URL     |(Mattermost内向きのWebHookの設定値)|
      |Mattermost Channel |(Mattermost内向きのWebHookの設定値)|
      |Display Watchers?  |Yes                                |
      |Post Issue Updates?|(有効)                             |
      |Post Wiki Updates? |(有効)                             |

Offline環境
------------------------------------------------------------
Offline環境ではpassengerがインターネット接続しようとして起動に失敗するため、
下記設定をdocker-compose.ymlのappに追加すること

``` yaml
(追加設定)
  environment:
    - PASSENGER_COMPILE_NATIVE_SUPPORT_BINARY=0
    - PASSENGER_DOWNLOAD_NATIVE_SUPPORT_BINARY=0
    - REDMINE_NO_DB_MIGRATE=true
```

``` yaml
# Redmineの設定
app:
  build: app
  volumes:
    - /opt/docker/redmine/volumes/app/usr/src/redmine/files:/usr/src/redmine/files
    - /opt/docker/redmine/volumes/app/opt/redmine/repos:/opt/redmine/repos
    - /etc/localtime:/etc/localtime:ro
  environment:
    - PASSENGER_COMPILE_NATIVE_SUPPORT_BINARY=0
    - PASSENGER_DOWNLOAD_NATIVE_SUPPORT_BINARY=0
    - REDMINE_NO_DB_MIGRATE=true
  ports:
    - "3000:3000"
  links:
    - db:postgres
```
