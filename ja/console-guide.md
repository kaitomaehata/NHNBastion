## Security > NHN Bastion > コンソール使用ガイド
NHN Bastionを利用してNHN Cloudのインスタンスへのアクセスを制御できます。この文書では、NHN CloudコンソールでNHN Bastionを作成し、アクセスが必要なインスタンスに接続する方法と、ユーザーとポリシー、リソース、履歴を管理する方法について説明します。
### はじめに

1. NHN Bastionを使用するためには、まず、NHN Bastionサービスを有効にします。
2. NHN Bastionはサービスを提供するため、API連携を通じてユーザーのリソースにアクセスできる権限が必要です。

### NHN Bastionの作成
![image](https://github.com/user-attachments/assets/bdcf129e-996a-44b1-8c17-09f4cddf9008)

1. **Security > NHN Bastion** に移動します。
2. 各項目を設定し、下部の**インスタンス作成**をクリックします。
    * **名前**(必須):複数のWebターミナルを利用する場合、Webターミナルを区別するための名前。
    * **アベイラビリティゾーン**(必須)：Webターミナルで使用するアベイラビリティゾーン   
    * **タイプ**(必須): Webターミナルのインスタンス性能
        * 性能はWebターミナルが同時に作成できるSSHセッション数を意味
    * **VPC**(必須): Webターミナルで使用するVPC
    * **サブネット**(必須): Webターミナルで使用するサブネット
    * **Floating IP**(任意): Webターミナルで使用するFloating IPの使用有無
    * **暗号化**(必須)：ログ暗号化保存の無
        * 対称鍵ID：Secure Key Managerサービスで管理する対称鍵ID
        * 暗号化設定のためには、事前にSecure Key Managerサービスで対称鍵を作成する必要があります。
        
> [参考]
> * 最初に作成されたWebターミナルはサービスを無効にするまでスペックの変更または削除を行うことができません。
> * インターネットゲートウェイが接続されたVPCに限り、Floating IPを接続できます。
> * Webターミナルで接続先とのSSH通信を許可すると接続先にアクセスできます。 (例：Security Groupsなど)
> * ログ暗号化対象：ユーザーID、メール、コマンド
> [注意]
> * Secure Key Managerサービスでログ暗号化に設定した 対称鍵をローテーションする場合、以前のバージョンの鍵をすぐに削除しないように注意が必要です。
> * Secure Key Managerサービスでログ暗号化に設定した 対称鍵を削除すると、暗号化された ログを復号できません。対称鍵を誤って削除しないように注意して管理する必要があります。

### インスタンス接続

<strong>ポリシー管理</strong>でユーザーにアクセスが許可されたインスタンスリストのみ表示され、アクセスが許可されたインスタンスにアクセスできます。

#### インスタンスへのアクセス

1. アクセスが必要なインスタンスで**接続**をクリックします。
2. インスタンスアクセスに利用するWebターミナルを選択します。
3. アクセスするインスタンスのIPが複数ある場合、アクセス可能なIPを選択します。
4. 認証方式を選択します。
    * SSHキー認証
        * インスタンスのOSアカウントを入力します。
        * インスタンス作成時に設定したキーをアップロードします。
    * パスワード認証
        * インスタンスのOSアカウントを入力します。
        * インスタンスのパスワードを入力します。
    * 一時的なSSHキー認証
        * インスタンスのOSアカウントを入力します。

> [注意]
> * インスタンスとSSH通信が可能なWebターミナルを選択する必要があります。
> * パスワード認証はOSでパスワードアクセスができるように設定する必要があります。
> * 一時的なSSHキー認証は、スクリプトをコピーしたWebターミナルを通じてのみアクセスできます。

### ユーザー管理

NHN Bastionサービスに権限があるユーザーリストを確認し、ユーザーグループを作成・管理できます。

#### ユーザーリスト
![image](https://github.com/jongwoo-kim-nhn/NHNBastion/assets/174567179/38558289-7b24-4030-899a-9a202b75a2e4)

NHN Bastionサービス利用権限を持つユーザーのリストです。ユーザーの権限および最後にインスタンスにアクセスした日時を確認できます。

#### グループ
![image](https://github.com/jongwoo-kim-nhn/NHNBastion/assets/174567179/99d6d052-bca4-4150-a55e-99d375de7dc1)

ユーザーグループを作成及び管理することができ、作成したグループは<strong>ポリシー管理</strong>タブでアクセス主体として登録できます。

* **+ グループ作成**: NHN Bastionサービスに権限を持つユーザーを追加または削除してグループを作成できます。 
* **コピー**:作成されたユーザーグループをコピーできます。
* **修正**:作成されたユーザーグループを修正できます。
* **削除**:選択したユーザーグループを削除できます。

### ポリシー管理
![image](https://github.com/user-attachments/assets/0a3683bb-5661-4867-83a6-6a8ae795116f)

接続対象に登録されたインスタンスを対象にアクセス制御ポリシーとコマンド制御ポリシーを設定できます。
各ポリシーには優先順位があり、優先順位が高い順にポリシーが適用されます。

[例]
下記の[表]のようにポリシーが適用されている場合、
* user AはInstance Aアクセス可能、 `shutdown`コマンドのみ使用不可、その他のコマンドは使用可能
* user AはInstance Bアクセス可能、 `cd`コマンドのみ使用可能、その他のコマンドは使用不可
* user BはInstance Aアクセス可能、 `reboot`コマンドのみ使用不可、その他のコマンドは使用可能
* user BはInstance Bアクセス不可

| 優先順位 | ユーザー | アクセス対象 | コマンドポリシー |
| ---- | --- | ---- | ------ |
| #1 | user A | Instance A | [禁止] shutdown |
| #2 | user A | Instance A, Instance B | [許可] cd |
| #3 | user B | Instance A | [禁止] reboot |

#### ポリシー作成
![image](https://github.com/jongwoo-kim-nhn/NHNBastion/assets/174567179/3d0c97b7-43fd-459c-95f3-a9873cc1f861)

* **名前**:ポリシーの名前を入力できます。
* **説明**:ポリシーの説明を入力できます。
* **ユーザー及びグループ**:ユーザー及びユーザーグループをアクセス主体として登録できます。
* **リソースとリソースグループ**
    * **全てのリソース**:接続対象に追加されたすべてのリソースをアクセス対象として登録します。
    * **リソース選択**:インスタンス、オートスケーリンググループ、リソースグループをアクセス対象として登録できます。
* **コマンド制御ポリシー
    * **許可**:登録されたコマンドのみ使用できます。 (ホワイトリスト方式)
    * **禁止**:登録されたコマンドの使用をブロックします。 (ブラックリスト方式)

> [注意]
> 下記のコマンドについては、コマンドポリシーの登録の有無に関わらず、全てブロックされます。
> * 迂回遮断コマンド: SSH, TELNET, SFTP, RCP, SCP, FTP, RSAP, RLOGINなど

#### ポリシー順序の変更
![image](https://github.com/jongwoo-kim-nhn/NHNBastion/assets/174567179/2342d3d9-a7f9-43d4-a474-0c514d5dda0b)

ポリシーの優先順位を変更できます。
   1. 選択したポリシーの順序を希望の優先順位に変更し、**修正**をクリックします。
   2. **順序変更後**で修正した優先順位のプレビューを確認します。プレビューは、修正したポリシーを基準に、前後のポリシーが一緒に表示されます。
   3. **保存**をクリックして、ポリシーの優先順位を変更します。

#### ポリシー管理

* **修正**:選択したポリシーの内容を修正できます。
* **コピー**:選択したポリシーをコピーできます。
* **削除**:選択したポリシーを削除できます。
* **詳細表示**:選択したポリシーの詳細を確認できます。

#### 一括登録
![image](https://github.com/user-attachments/assets/0f6e47fe-f870-4a8a-a08b-1749a39decef)

提供されるテンプレートを利用してポリシーを一括登録することができ、作成されているポリシーをダウンロードしてバックアップできます。
* **一括登録**：テンプレートを利用してポリシーを一括でアップロード提供
* **ポリシー一括ダウンロード**：現在適用されているポリシーリストのダウンロードを提供

### リソース管理

#### インスタンス管理
![image](https://github.com/user-attachments/assets/5313b554-47f7-4b54-acbe-0f6b29424a39)

**リソース管理 > インスタンス管理**タブでは、プロジェクト内に登録されているインスタンスを接続対象として追加できます。
登録されたインスタンスを対象にアクセス制御ポリシー及びコマンド制御ポリシー機能を提供します。

* **+ 追加**:プロジェクト内に存在するインスタンスを選択して接続対象として追加できます。
* **削除**:選択したインスタンスを接続対象から削除できます。
* **全体削除**:登録された全てのインスタンスを接続対象から削除できます。

* **接続設定**
    * 選択したインスタンスのセッションタイムアウトを変更できます。
        * 基本設定:環境設定に適用されているセッションタイムアウト
    * 選択したインスタンスの接続ポートを変更できます。
        * 基本設定:環境設定に適用されている接続ポート

* **外部リソース**
   * **登録**：レガシー環境のサーバーや他プロジェクトのインスタンスなどを手動で登録できます。
   ![image](https://github.com/user-attachments/assets/1ab9806a-061c-478b-b9e0-38a5dabe4f50)

   * **一括登録**：外部リソースをテンプレートを利用して一括登録できます。
   ![image](https://github.com/user-attachments/assets/6806a22f-8a6a-4d38-b42e-1aa857004670)

> [注意]
> 登録された外部リソースについては、IPを基準にWebターミナルからSSH通信を試みます。Webターミナルから目的地サーバーまで通信が可能な環境でのみサービスを利用できます。
     
* **自動登録**：プロジェクト内に存在する全てのインスタンスが自動的に接続対象に追加されます。インスタンス作成/削除時にも自動的に反映され、接続対象に追加されます。

> [注意]
> インスタンス作成または削除に対する変更事項は最大5分後に反映される場合があります。
* **サービス利用状況ダウンロード**：現在登録されているサービス対象のリストをダウンロードできます。

#### Webターミナル管理
![image](https://github.com/user-attachments/assets/237364f8-f54b-4e32-9f6c-5a5dfef6574e)

**リソース管理 > Webターミナル管理**で、インスタンスアクセスに必要なターミナルとBastionを提供するWebターミナルインスタンスを作成/管理できます。

* **+ 作成**
    * **名前**(必須):複数のWebターミナルを利用する場合、Webターミナルを区別するための名前
    * **タイプ**(必須): Webターミナルのインスタンス性能
        * **性能**:性能別の違いは、Webターミナルが同時に作成できるSSHセッションの数
    * **VPC**(必須): Webターミナルで使用するVPC
    * **サブネット**(必須): Webターミナルで使用するサブネット
    * **Floating IP**(任意): Webターミナルで使用するFloating IPの使用有無
* **削除**:選択したWebターミナルを削除できます。

> [注意]
> サービス有効化時に最初に作成されたWebターミナルは削除できません。削除するにはサービスを無効化する必要があります。

* **Floating IP**
  
  ![image](https://github.com/jongwoo-kim-nhn/NHNBastion/assets/174567179/5daf1846-662a-4e0f-bfc7-96120c4fcedf)
    * WebターミナルのFloating IPを使用するかどうかを設定できます。
    * Network Firewallサービスを利用する顧客がDNAT機能を利用してWebターミナルにパブリックIPを割り当てる場合、**リダイレクト**機能を**使用**に設定してWebターミナルのパブリックIPを入力できます。
    * 社内DNSを利用する顧客は、**リダイレクト** 機能を**使用**に設定してWebターミナルのドメインアドレスを入力できます。
    
* **IPアクセス制御**
  
     ![image](https://github.com/user-attachments/assets/0559ba4a-960c-42cc-918f-37c83fd95ac0)
    * Webターミナルにアクセスが必要なCIDRを入力できます。

> [参考]
> IPアクセス制御はホワイトリスト方式で提供されます。

* **静的ルーティング更新**

   * サブネットに適用された 静的ルーティングポリシーをWebターミナルに適用します。
   
* **スクリプト**
  
  ![image](https://github.com/jongwoo-kim-nhn/NHNBastion/assets/174567179/d62cab1a-a01b-42f3-aca3-0afc4577c9e9)
    * 一時的なSSHキーを利用したアプローチを利用するために、対象インスタンスで実行する必要があるスクリプトを提供します。

> [注意]
> * 一時的なSSHキーによるアプローチは、選択したWebターミナルでのみ適用され、他の経路でアクセスする場合は提供されません。
> * Webターミナルの**IPアクセス制御**に接続対象インスタンスIPが追加されている必要があります。
> * 接続対象インスタンスのSecurity GroupsでWebターミナルのIPに443ポートアウトバウンドポリシーが追加されている必要があります。

#### リソースグループ

**リソース管理 > リソースグループ**タブでは、接続対象に登録されているインスタンスグループを作成及び管理できます。

![image](https://github.com/jongwoo-kim-nhn/NHNBastion/assets/174567179/9d4a9853-d841-4f02-9aa3-c1f88e96d708)
* **+ グループ作成**:接続対象に登録されているインスタンスを追加または削除してグループを作成できます。
* **コピー**:リソースグループを選択してコピーできます。
* **修正**:リソースグループを修正できます。
* **削除**:リソースグループを選択して削除できます。

### 履歴管理

NHN Bastionサービスを利用してインスタンスにアクセスした履歴を管理できます。ログ照会期間は最大 一週間まで設定できます。
履歴は最大6か月間保管され、6か月以上保管が必要な場合は、<strong>環境設定 >ログ管理</strong>でObject Storageにバックアップできます。

#### リアルタイムセッション
![image](https://github.com/user-attachments/assets/ca70cf06-2d74-4ca7-ab57-51e02ec6b083)

リアルタイムでインスタンスと接続されているセッションを確認することができ、接続を遮断できます。

* **詳細表示**：接続されているユーザーセッションの詳細情報を確認できます。
* **セッションブロック**：接続されているユーザーセッションをブロックします。

#### ユーザーアクセス履歴
![image](https://github.com/user-attachments/assets/2defa233-35b5-4dde-82bf-7e0238bc055b)

ユーザーがインスタンスにアクセスした履歴を確認することができ、検索条件と接続時間を利用して目的の履歴を照会できます。

* **詳細表示**
    * **基本情報**:選択した履歴のセッション情報を確認できます。
    * **ログ**:選択した履歴のセッションで使用したコマンドの履歴を確認できます。

#### コマンド使用履歴
![image](https://github.com/user-attachments/assets/1c6446b9-e452-49fa-837d-5d5122191678)

ユーザーが使用したコマンドを確認することができ、検索条件と接続時間を利用して目的のコマンドの使用履歴を照会できます。

* **コマンド選択**：登録したコマンドを選択して検索条件に追加できます。
* **詳細表示**
    * **基本情報**:選択した履歴のセッション情報を確認できます。
    * **ログ**:選択した履歴のセッションで使用したコマンド履歴を確認できます。

#### ファイル送信履歴
![image](https://github.com/user-attachments/assets/27313eff-6b28-4908-943a-c6c3d621df57)

ユーザーがインスタンスにファイルアップロード/ダウンロードした 履歴を確認することができ、検索条件と接続時間を利用して目的の履歴を照会できます。

* **詳細表示**
    * **基本情報**：選択した履歴のセッション情報を確認できます。
    * **ログ**:選択した履歴のセッションで使用した コマンド履歴を確認できます。

### 環境設定
![image](https://github.com/user-attachments/assets/b88fbdf3-a16c-426a-8a5f-a71ced18cc98)

#### セッションタイムアウト

インスタンスアクセス時に、Webターミナルで提供するセッションタイムアウト時間を設定できます。

> [参考]
> Webターミナルで提供するセッションタイムアウト設定なので、OSで設定したセッションタイムアウトとは別に動作します。

#### 最大接続セッション

ユーザーが同時に接続できる最大セッション数を設定できます。

#### ログ管理

NHN Bastionサービスで提供するログを顧客のObject Storageにバックアップできます。

* **アクセスキー**: S3 API認証情報アクセスキー
* **シークレットキー**: S3 API認証情報秘密鍵
* **バケット名**:作成したバケット名
* **エンドポイント**
  * 韓国(パンギョ)リージョン: https://kr1-api-object-storage.nhncloudservice.com
  * 韓国(ピョンチョン)リージョン: https://kr2-api-object-storage.nhncloudservice.com
  * 韓国(光州)リージョン: https://kr3-api-object-storage.nhncloudservice.com
* **リージョン**
  * 韓国(パンギョ)リージョン: KR1
  * 韓国(ピョンチョン)リージョン: KR2
  * 韓国(光州)リージョン: KR3
 
> [参考]
> ログ暗号化を使用する場合ログは復号してObject Storageにバックアップされます。
> バックアップを設定した時点のログからObject Storageにバックアップされます。以前に保存されたログはバックアップされません。

> [注意]
> 最初に作成されたWebターミナルが位置するVPCにインターネットゲートウェイがない場合は、他のリージョンのObject Storageバックアップがサポートされません。

#### 接続ポート管理

インスタンスアクセスに使用するSSHポートを指定できます。

> [参考]
> Webターミナルからアクセス対象インスタンスに接続する際に利用するポートで、OSで設定したSSHポートに設定する必要があります。

#### 暗号化

暗号化が有効かどうかと、適用された 対称鍵IDを確認できます。
Secure Key Managerでキーローテーションを実行した場合、[キーローテーション] ボタンを使用して、以前のバージョンのキーを最新バージョンに更新できます。

> [注意]
> Secure Key Managerでキーローテーションを実行した後、NHN Bastionサービスでキーを更新しない場合、ログ照会時にエラーが発生する可能性があります。

#### NHN Bastionの削除

NHN Bastionサービスで作成されたすべてのリソースを削除します。

> [注意]
> NHN Bastionサービス利用中に作成されたデータとサービス内のすべてのリソースが削除され、一度削除された情報は復元できません。


### Webターミナル
ブラウザベースのWebターミナルを提供し、ファイルアップロード/ダウンロード機能を提供します。

#### ファイル転送
右矢印ボタンをクリックしてファイルナビゲーターを実行できます。ファイルナビゲーターを通じて、希望のパスのファイルをアップロードまたはダウンロードできます。
右矢印ボタンをクリックしてフォントサイズを調整できます。

>[参考]
> 最初に接続したOSアカウントの権限を基準にファイル転送が可能で、suコマンドによるアカウントの変更は反映されません。
> ドラッグアンドドロップでファイルをアップロードする場合、現在のディレクトリパスに関係なく ホームディレクトリに保存されます。
