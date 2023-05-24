
NGINX OSS の利用
=================

1. NGINX OSSのインストール
----

| 本ページに記載する手順に従ってNGINX Plus をインストールします
| 参考： `Installing a Prebuilt Ubuntu Package from the Official NGINX Repository <https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-open-source/#installing-a-prebuilt-ubuntu-package-from-the-official-nginx-repository>`__

- NGINX OSSのインストール方法は大きく3つあります。この手順では ``NGINXが提供するPrebuild Binary`` の利用方法です。

   それぞれの違いは以下の通りです
   +-----------------------+-----------------------+
   |利用方法               |管理主体               |
   +-----------------------+-----------------------+
   |NGINX Prebuild         |F5/NGINX※             |
   +-----------------------+-----------------------+
   |Linux Distri Prebuild  |Linux, OSSコミュニティ |
   +-----------------------+-----------------------+
   |Source Code から Build |OSSコミュニティ        |
   +-----------------------+-----------------------+

- NGINX OSSは2つのバージョンがあります

  - ``Mainline`` - 最新の機能追加、バグFixを含んだバージョンです。更に実験的な機能や、新たなバグを含む場合があります
  - ``Stable`` - すべての最新機能を含んでいるものではなく、またCriticalなバグFixを含んだバージョンです。安定バージョンです

1. コマンドの実行
~~~~~~~~

必要となるパッケージをインストールします

.. code-block:: cmdin

   sudo apt update
   sudo apt install -y curl gnupg2 ca-certificates lsb-release debian-archive-keyring


NGINX OSSに必要なリポジトリに利用する鍵を取得します

.. code-block:: cmdin

  curl https://nginx.org/keys/nginx_signing.key | gpg --dearmor \
    | sudo tee /usr/share/keyrings/nginx-archive-keyring.gpg >/dev/null

レポジトリの情報を追加します。

``mainline`` をインストールする場合は以下を指定してください

.. code-block:: cmdin

  echo "deb [signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg] \
  http://nginx.org/packages/mainline/ubuntu `lsb_release -cs` nginx" \
      | sudo tee /etc/apt/sources.list.d/nginx.list

``stable`` をインストールする場合は以下を指定してください

.. code-block:: cmdin

  echo "deb [signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg] \
  http://nginx.org/packages/ubuntu `lsb_release -cs` nginx" \
      | sudo tee /etc/apt/sources.list.d/nginx.list
  

aptコマンドの設定情報を取得します

.. code-block:: cmdin

  echo -e "Package: *\nPin: origin nginx.org\nPin: release o=nginx\nPin-Priority: 900\n" \
      | sudo tee /etc/apt/preferences.d/99nginx


パッケージ情報を更新します

.. code-block:: cmdin

   sudo apt update

3. NGINX パッケージのインストール
~~~~~~~~

.. code-block:: cmdin

  sudo apt install nginx

NGINX Plus と NGINX OSS は利用できるDirectiveやモジュールが異なります。
その点を考慮し、 `NGINXの基礎 <https://f5j-nginx-plus-lab1.readthedocs.io/en/latest/class1/module2/module2.html#id2>`__ の内容を参考に動作を確認してください。
