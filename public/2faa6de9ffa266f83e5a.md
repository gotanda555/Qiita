---
title: AWS IAMユーザー・グループ 概要 ~ 作成手順の解説
tags:
  - AWS
  - IAM
private: false
updated_at: '2022-11-11T21:52:04+09:00'
id: 2faa6de9ffa266f83e5a
organization_url_name: null
---
# はじめに
IAMユーザーとIAMユーザーグループを学ぶ機会があったため、概要から作成手順までまとめました。

# IAMユーザーとは
IAMユーザーとは、AWSを使用する人に発行するユーザーです。
ポリシーを付与することで、行動に制限をかけることができます。

https://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/id_users.html

## ルートユーザーとの違い
AWSを操作できる意味では一緒ですが、ルートユーザーの方が権限が大きいため、操作できる幅が広いです。
（詳細は、以下のURLから飛んでみてください。）

https://docs.aws.amazon.com/ja_jp/general/latest/gr/root-vs-iam.html

# 作成手順
1. IAMユーザーグループの作成
2. IAMユーザーの作成
3. IAMユーザーでログインし、MFA（2段階認証）を有効にする

:::note
IAMユーザーに直接ポリシーを付与することも可能です。
ただ、グループを先に作成後、グループにポリシーを付与して、ユーザーを紐づけるのが一般的と言われています。
:::

## 1. IAMユーザーグループの作成
### IAMユーザーグループとは
IAMユーザーグループとは、IAMユーザーをグルーピングしたものです。
ユーザーグループを使用すると、複数のユーザーに対してアクセス許可を指定でき、それらのユーザーのアクセス許可を容易に管理することができます。

https://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/id_groups.html

### 作成
まず、IAMのコンソールへ移動する。
![スクリーンショット 2022-11-05 15.29.30.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2849898/46b4970d-3847-920d-42ac-c230b1c18f32.png)

次に、サイドバーにある「User groups」を押して、User groups画面へ移動する。
![Group 1.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2849898/9c89ddd5-ecf9-2d8e-bf5a-65142c8b10e3.png)

上記画像の右上にある「Create group」ボタンを押して、Create user group画面へ移動する。
![スクリーンショット 2022-11-05 15.40.52.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2849898/fb117a8f-4d8c-3eb6-7017-acdef6b3a263.png)

上記画面で、それぞれ以下の設定を行う。
#### Name the group
グループ名を決める。任意の値で問題ないが、役割が明確になる名前が相応しい。
（例：管理者のグループであれば、「Administrators」など）

#### Add users to the group - Optional
グループに所属するIAMユーザーを選択する。
既にユーザーがいれば選択しても問題ないが、IAMユーザー作成時に既存のユーザーグループに割り当てることも可能。

#### Attach permissions policies - Optional
グループに相応しいポリシーを設定する。
ここは、条件によって変わるため、その時々の条件に沿ってポリシーを選択するのが良い。

主には、以下の2つが多いイメージ
- 管理者の職務機能：AdministratorAccess
- 読み取り専用アクセス：ReadOnlyAccess

設定が終われば、「Create group」を押して、作成完了。


## 2. IAMユーザーの作成
サイドバーにある「Users」を押して、Users画面へ移動する。
![Group 2.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2849898/332cac56-d9ee-bafb-f588-1fc260623403.png)

上記画像の右上にある「Add users」ボタンを押して、Add user画面へ移動する。
![スクリーンショット 2022-11-05 16.10.55.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2849898/cf9797c3-8971-2937-d6a3-7af95b1ff366.png)

あとは、画面の順番に沿って、設定を行なっていく。
#### Step1
以下をそれぞれ設定する
##### Set user details
IAMユーザーの名前を設定する。
「Add another user」を押すと、複数のユーザーを設定することができる。

##### Select AWS access type
AWSへのアクセス方法を以下2つから選択する。（両方を選択することも可能。）
- Access key - Programmatic access
- Password - AWS Management Console access

Access Key や Passwordで自動生成パスワードを選択していた場合、ユーザーを作成し終わった後に提供される。
個人的には、Access Keyは後でも作成できるため、Passwordを設定することが多い。

設定できたら、「Next: Permissions」ボタンを押す。

#### Step2
「Add user to group」で、先程作成したユーザーグループに追加するように設定する。
（「Attach existing policies directly」などで、直接ポリシーを設定することも可能。）
![Group 3.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2849898/8d0f58fa-374a-a53d-83af-a0856376acb7.png)

チェックを入れたら、「Next: Tags」ボタンを押す。

#### Step3
タグを設定する。
![スクリーンショット 2022-11-05 16.40.08.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2849898/f47b8440-3ee0-08a9-50dc-6327b0788d74.png)

設定できたら、「Next: Review」を押す。

#### Step4
最後に、今まで設定したのに間違いがないか、しっかり確認する。
![Group 4.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2849898/f1293ce4-3e01-e256-01d1-0aa310a590f8.png)

問題なければ、「Create user」ボタンを押す。

#### Step5
作成されたユーザー情報が、画面に表示される。
ここで、Step1でアクセスキーや自動生成パスワードを選択していれば、表示される。

:::note warn
シークレットキーに関しては、後で確認することができないため、しっかり管理しておきましょう。
:::

## 3. IAMユーザーでログインし、MFA（2段階認証）を有効にする
作成したIAMユーザーのセキュリティを高めるために、2段階認証が必須とされている。
（設定していなくても、ログインは可能。）

まず、Userの画面へいき、設定を行いたいユーザー名をクリックする。
「Sumry」画面の、「Security credentials」タブを押す。
![Group 5.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2849898/49098023-2dbd-3e84-48dc-8b9ef1e24ab5.png)

「Assigned MFA device」の「Manage」を押すと、モーダルが表示される。
今回はアプリのAuthyを使用したいため、「Virtual MFA device」を選択して、「Continue」ボタンを押す。
![スクリーンショット 2022-11-05 17.07.48.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2849898/0357a5cb-3d15-26d4-c178-eb93e7eaea3a.png)

※ Authyの使い方は省略するが、以下が公式HPになるため、要確認。

https://authy.com/

AuthyでQRコードを読み込み、発行されるMFA code（6桁の数字）を2回入力する。
![スクリーンショット 2022-11-05 17.16.04.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2849898/cd85aa3a-5da0-191e-6cb3-b6c82897a079.png)

2回MFA codeを入力できたら、「Assign MFA」ボタンを押して、2段階認証が登録される。
これで、次回からログインする際に、MFA codeが求められるようになる。


# 注意点
**アクセスキーとシークレットキーの管理には、注意しましょう！**
漏洩すると、以下のことが想定されます。

- 迷惑メールの送信
- 多額の利用費請求
- 情報漏洩　・・・など

特に権限が大きいルートユーザーのアクセスキーなどが漏洩すると、大変なことになるので、アカウントのセキュリティはしっかりしましょう。

:::note warn
IAMユーザーのアクセスキーが漏洩されたとしても、そのIAMユーザーのポリシーが、読み取り専用などであれば、被害はそこまで大きく出ません。
ただ何かあるかはわからないため、管理はしっかりしておきましょう。
:::

# まとめ
IAMユーザーやグループは、今までなんとなくでしか触っていなかったですが、かなり奥が深いことがわかりました。
引き続き、アウトプットは継続していこうと思います。

記事をご覧いただき、ありがとうございました。
