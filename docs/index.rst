
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

[これから始めるNGINX技術解説～基本編](https://www.nginx.co.jp/resources/webinars/nginx-back-to-basic-jp/)

[これから始めるNGINX技術解説～基本編 Part2](https://www.nginx.co.jp/resources/webinars/nginx-back-to-basic-2-jp/)

ラボ環境 (UDF(Unified Demonstration Framework)) コンポーネントへの接続
====

| 弊社が提供するLAB環境を使って動作を確認いただきます。   
| ラボ環境を起動する等、一部ブラウザを使って操作します。   
| Google ChromeがSupportブラウザとなります。その他ブラウザでは正しく動作しない場合があることご了承ください。   
| 参照：[UDF Supported Browsers and Clients](https://help.udf.f5.com/en/articles/3470266-supported-browsers-and-clients)

| 無料トライアルの申し込みリンクは以下となります。
| 対象のソフトウェアのリンクをクリックしてください。

.. NOTE::
   | RDPのUser名、パスワードはDETAILSをクリックし、GeneralのタブのCredentialsの項目を参照ください   
   | `user` でログインしてください

   .. image:: ./media/udf_jumpbox.png
      :width: 400

   .. image:: ./media/udf_jumpbox2.png
      :width: 400


Windows Jump Hostへログインいただくと、SSH Clientのショートカットがありますので、そちらをダブルクリックし `ubuntu01` へ接続ください

   .. image:: ./media/putty_icon.jpg
      :width: 400

   .. image:: ./media/putty_menu.jpg
      :width: 400




.. toctree::
   :titlesonly:
   :caption: コンテンツ
   :glob:

   content*/content*
