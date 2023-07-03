
NGINX OSS の利用
=================

1. NGINX OSSのインストール
----

| 本ページに記載する手順に従って ``NGINX OSS`` をインストールします
| 参考： `Installing a Prebuilt Ubuntu Package from the Official NGINX Repository <https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-open-source/#installing-a-prebuilt-ubuntu-package-from-the-official-nginx-repository>`__

- NGINX OSSのインストール方法は大きく3つあります。この手順では ``NGINXが提供するPrebuild Binary`` の利用方法です。

それぞれの違いは以下の通りです

+-----------------------+-----------------------+
|利用方法               |管理主体               |
+-----------------------+-----------------------+
|NGINX Prebuild         |NGINX                  |
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

2. NGINX パッケージのインストール
~~~~~~~~

.. code-block:: cmdin

  sudo apt install nginx


インストールしたパッケージの情報の確認します

| 参考となる記事はこちらです。
| `K72015934: Display the NGINX software version <https://support.f5.com/csp/article/K72015934>`__

.. code-block:: cmdin

  nginx -v

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

  nginx version: nginx/1.25.1

``-V`` (大文字)　を指定することによりパッケージが利用するOpenSSLの情報や、configureのオプションを確認できます。

.. code-block:: cmdin

  nginx -V

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

  nginx version: nginx/1.25.1
  built by gcc 9.3.0 (Ubuntu 9.3.0-10ubuntu2)
  built with OpenSSL 1.1.1f  31 Mar 2020
  TLS SNI support enabled
  configure arguments: --prefix=/etc/nginx --sbin-path=/usr/sbin/nginx --modules-path=/usr/lib/nginx/modules --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --pid-path=/var/run/nginx.pid --lock-path=/var/run/nginx.lock --http-client-body-temp-path=/var/cache/nginx/client_temp --http-proxy-temp-path=/var/cache/nginx/proxy_temp --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp --http-scgi-temp-path=/var/cache/nginx/scgi_temp --user=nginx --group=nginx --with-compat --with-file-aio --with-threads --with-http_addition_module --with-http_auth_request_module --with-http_dav_module --with-http_flv_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_mp4_module --with-http_random_index_module --with-http_realip_module --with-http_secure_link_module --with-http_slice_module --with-http_ssl_module --with-http_stub_status_module --with-http_sub_module --with-http_v2_module --with-http_v3_module --with-mail --with-mail_ssl_module --with-stream --with-stream_realip_module --with-stream_ssl_module --with-stream_ssl_preread_module --with-cc-opt='-g -O2 -fdebug-prefix-map=/data/builder/debuild/nginx-1.25.1/debian/debuild-base/nginx-1.25.1=. -fstack-protector-strong -Wformat -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2 -fPIC' --with-ld-opt='-Wl,-Bsymbolic-functions -Wl,-z,relro -Wl,-z,now -Wl,--as-needed -pie'

またUbuntuの環境では以下サンプルのようにパッケージの詳細を確認することが可能です。ラボ環境でコマンドを入力する際にはVersionの指定を適宜変更してください

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:
  :emphasize-lines: 2-3,8,6

  # sudo apt show nginx
  Package: nginx
  Version: 1.25.1-1~focal
  Priority: optional
  Section: httpd
  Maintainer: NGINX Packaging <nginx-packaging@f5.com>
  Installed-Size: 3524 kB
  Provides: httpd, nginx, nginx-r1.25.1
  Depends: libc6 (>= 2.28), libcrypt1 (>= 1:4.1.0), libpcre2-8-0 (>= 10.22), libssl1.1 (>= 1.1.1), zlib1g (>= 1:1.1.4), lsb-base (>= 3.0-6), adduser
  Conflicts: nginx-common, nginx-core
  Replaces: nginx-common, nginx-core
  Homepage: https://nginx.org
  Download-Size: 1001 kB
  APT-Manual-Installed: yes
  APT-Sources: http://nginx.org/packages/mainline/ubuntu focal/nginx amd64 Packages
  Description: high performance web server
   nginx [engine x] is an HTTP and reverse proxy server, as well as
   a mail proxy server.


- ``2~3,8行目`` : 指定したNGINXのパッケージであることが確認できます
- ``6行目`` : MaintainerとしてF5の情報が確認できます

NGINX Plus と NGINX OSS は利用できるDirectiveやモジュールが異なります。
その点を考慮し、 `NGINXの基礎 <https://f5j-nginx-plus-lab1.readthedocs.io/en/latest/class1/module2/module2.html#id2>`__ の内容を参考に動作を確認してください。
