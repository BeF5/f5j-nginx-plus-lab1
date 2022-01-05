
実施環境
====

- 事前にラボ環境へのInviteを行っておりますので、メールをご確認ください
- 利用するコマンド： kubectl git , jq , sudo, curl
- NGINX Trialライセンスの取得、ラボ実施ユーザのHome Directoryへ配置

座学資料
====

このラボはNGINX Plusのインストールから各種設定を行っていただけます。

NGINX Plusの基本的な動作や仕様について紹介しております。

| 基本的な解説資料は以下を参照してください。  
| (このラボはこのセミナーでご紹介した内容を一部抜粋しております)


セミナー資料
----

[これから始めるNGINX技術解説～基本編](https://www.slideshare.net/Nginx/nginx-nginx-back-to-basic-in-jp)
 (2.1～2.3 , 3.1～3.5に該当)   

[これから始めるNGINX技術解説～基本編 Part2](https://www.slideshare.net/Nginx/nginx-back-to-basic-2-part-2-japanese-webinar)
 (3.6～3.9に該当) 


Webinar(プレゼンテーション・デモ)
----

`これから始めるNGINX技術解説～基本編 <https://www.nginx.co.jp/resources/webinars/nginx-back-to-basic-jp/>`_

`これから始めるNGINX技術解説～基本編 Part2 <https://www.nginx.co.jp/resources/webinars/nginx-back-to-basic-2-jp/>`_


ラボ環境 (UDF(Unified Demonstration Framework)) コンポーネントへの接続
====

| 弊社が提供するLAB環境を使って動作を確認いただきます。   
| ラボ環境を起動する等、一部ブラウザを使って操作します。   
| Google ChromeがSupportブラウザとなります。その他ブラウザでは正しく動作しない場合があることご了承ください。   
| 参照：`UDF Supported Browsers and Clients <https://help.udf.f5.com/en/articles/3470266-supported-browsers-and-clients>`_


Windows Jump HostへのRDP接続
----

Windows Jump HostからCLIの操作を行う場合、以下タブからRDP Clientファイルをダウンロードいただき接続ください

   .. image:: ./media/udf_jumpbox.png
      :width: 200

.. NOTE::
   | RDPのUser名、パスワードはDETAILSをクリックし、GeneralのタブのCredentialsの項目を参照ください   
   | `user` でログインしてください

   | .. image:: ./media/udf_jumpbox_loginuser.png
   |    :width: 200
   |    :align: left
   | 
   | .. image:: ./media/udf_jumpbox_loginuser2.png
   |    :width: 200
   |    :align: left

Windows Jump Hostへログインいただくと、SSH Clientのショートカットがありますので、そちらをダブルクリックし `ubuntu01` へ接続ください

   | .. image:: ./media/putty_icon.jpg
      :width: 50

   | .. image:: ./media/putty_menu.jpg
      :width: 200


Linux Hostへの接続 (Jump Host を利用しない場合)
----

   .. image:: ./media/udf_ssh_direct.jpg
      :width: 200

``ubuntu01`` へのSSH接続は、Jump Host経由 または、SSH鍵認証を用いて接続可能です。SSH鍵の登録手順は以下を参照ください
***SSH鍵を登録頂いていない場合、SSHはグレーアウトします***

`UDF LAB SSH鍵登録マニュアル <https://github.com/hiropo20/partner_nap_workshop_secure/blob/main/UDF_SSH_Key.pdf>`_
(ラボで必要となる場合、閲覧可に変更します)

NGINX Plus の動作
====

1. NGINX Plusのインストール (15min)
----

| 本ページに記載する手順に従ってNGINX Plus をインストールします
| 参考：`Installing NGINX Plus on Ubuntu <https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-plus/#installing-nginx-plus-on-ubuntu>`_


.. NOTE::
   | 手順確認の目的で、NGINX Plusの他、NGINX App Protect WAF、NGINX App Protect Dosのインストール手順も示しています。
   | ただし、本ラボでセキュリティ機能の確認はありません


NGINX Licenseファイルのコピー
^^^^

| ライセンスファイルをコピーしてください
| ファイルがラボ環境に配置されていない場合、トライアルを申請し証明書と鍵を取得してください   
| トライアルの申請方法は `トライアル申請方法 <https://github.com/hiropo20/nginx_how_to_get_plus_trial`_ を参照してください


取得したライセンスファイルを``Jump Host``にコピーした後、``ubuntu-01``に送信するために``pscp``をご利用いただくことが可能です。以下コマンドを参考にご利用ください。コマンドプロンプト、powershellなどのターミナルから実行いただけます

.. code::bash
   コマンド: pscp -i <SSHで利用する公開鍵> <送付するファイル> <宛先>
   pscp -i .\.ssh\id_rsa-putty.ppk <送信するファイル> ubuntu@10.1.1.7:/home/ubuntu

.. code::bash
   sudo mkdir -p /etc/ssl/nginx
   sudo cp ~/nginx-repo.crt /etc/ssl/nginx/
   sudo cp ~/nginx-repo.key /etc/ssl/nginx/

.. toctree::
   :titlesonly:
   :caption: コンテンツ
   :glob:

   content*/content*
