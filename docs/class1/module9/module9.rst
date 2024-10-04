補足情報
####

Tips1. NGINX Plus の Offline Install
====

NGINX Plus Subscriptionをご契約のお客様は、MyF5より各OSに対応したNGINX Plusや、Moduleをダウンロードすることが可能となります。
それらを利用し、OfflineのInstallが可能となります。

   .. image:: ./media/myf5_product_download.png
      :width: 400

MyF5を利用せずファイルを取得する場合、 `Tips2. Unprivilege(非特権)ユーザのInstall <#tips2-unprivilege-install>`__ の ``NGINX Plus Binaryの取得`` を参照してください。

1. Offline Install
----

パッケージのインストール

.. code-block:: cmdin

  # Ubuntu / Debian
  sudo dpkg -i  nginx-plus_28-1~focal_amd64.deb

  # RedHat / CentOS / Oracle Linux / Rocky Linux / Alma Linux
  sudo rpm -ivh nginx-plus-28-1.el8.ngx.x86_64.rpm

バージョンを確認します

.. code-block:: cmdin

  nginx -v

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

  nginx version: nginx/1.23.2 (nginx-plus-r28)

NGINX を起動します

.. code-block:: cmdin

  sudo nginx

動作を確認します

.. code-block:: cmdin

  curl localhost | grep title

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

  <title>Welcome to nginx!</title>

Tips2. Unprivilege(非特権)ユーザのInstall
====

こちらの内容は、 `NGINX Plus Unprivileged Installation <https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-plus/#nginx-plus-unprivileged-installation>`__ を参照しています。
各種作業手順や、コマンドの出力結果をまとめます。各種内容を参考にご確認ください。

こちらのスクリプトの実行(特にfetch / list)では ``wget`` コマンドを使用しますので、コマンドを実行するホストでは予め取得・インストールしてください。

1. Install Scriptの取得
----

Unprivilege Installに必要となるScriptを取得します

.. code-block:: cmdin

  wget https://raw.githubusercontent.com/nginxinc/nginx-plus-install-tools/main/ngxunprivinst.sh

  # wgetがない場合
  # curl https://raw.githubusercontent.com/nginxinc/nginx-plus-install-tools/main/ngxunprivinst.sh -o ngxunprivinst.sh


実行権限の付与

.. code-block:: cmdin

  chmod +x ngxunprivinst.sh
  
2. NGINX Plus Binaryの取得
----

| ライセンスファイルをコピーしてください
  ファイルがラボ環境に配置されていない場合、トライアルを申請し証明書と鍵を取得してください
| トライアルの申請方法は\ `トライアル申請方法 <http://f5j-nginx-plus-trial.readthedocs.io/>`__\ を参照してください

.. code-block:: cmdin

  cp ~/nginx-repo.* ./


NGINX Plus Binary の取得

.. code-block:: cmdin

  ./ngxunprivinst.sh fetch -c nginx-repo.crt -k nginx-repo.key

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

  Downloading nginx-plus_28-1~focal_amd64.deb...
  Downloading nginx-plus-module-auth-spnego_28%2B1.1.0-2~focal_amd64.deb...
  Downloading nginx-plus-module-brotli_28%2B1.0.0-1~focal_amd64.deb...
  Downloading nginx-plus-module-encrypted-session_28%2B0.09-1~focal_amd64.deb...
  Downloading nginx-plus-module-fips-check_28%2B0.1-2~focal_amd64.deb...
  Downloading nginx-plus-module-geoip_28-1~focal_amd64.deb...
  Downloading nginx-plus-module-geoip2_28%2B3.4-1~focal_amd64.deb...
  Downloading nginx-plus-module-headers-more_28%2B0.34-2~focal_amd64.deb...
  Downloading nginx-plus-module-image-filter_28-1~focal_amd64.deb...
  Downloading nginx-plus-module-lua_28%2B0.10.22-2~focal_amd64.deb...
  Downloading nginx-plus-module-ndk_28%2B0.3.2-1~focal_amd64.deb...
  Downloading nginx-plus-module-njs_28%2B0.7.10-1~focal_amd64.deb...
  Downloading nginx-plus-module-njs_28%2B0.7.9-1~focal_amd64.deb...
  Downloading nginx-plus-module-opentracing_28%2B0.27.0-1~focal_amd64.deb...
  Downloading nginx-plus-module-passenger_28%2B6.0.15-1~focal_amd64.deb...
  Downloading nginx-plus-module-perl_28-1~focal_amd64.deb...
  Downloading nginx-plus-module-prometheus_28%2B1.3.4-1~focal_amd64.deb...
  Downloading nginx-plus-module-rtmp_28%2B1.2.2-1~focal_amd64.deb...
  Downloading nginx-plus-module-set-misc_28%2B0.33-1~focal_amd64.deb...
  Downloading nginx-plus-module-subs-filter_28%2B0.6.4-1~focal_amd64.deb...
  Downloading nginx-plus-module-xslt_28-1~focal_amd64.deb...

3. NGINX Plus の Install
----

NGINXを展開するフォルダを必要に応じて作成

.. code-block:: cmdin

  mkdir tmp
  cd tmp/

特権を持たないユーザでのインストール

.. code-block:: cmdin

  ./ngxunprivinst.sh install -p ./tmp/ nginx-plus-28-1.el8.ngx.x86_64.rpm

インストールが実行後、Promptに下がって ``y`` を入力してください

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

  /home/centos/tmp already exists. Continue? {y/N}
  y << y を入力
  Installation finished. You may run nginx with this command:
  /home/centos/tmp/usr/sbin/nginx -p /home/centos/tmp/etc/nginx -c nginx.conf -e /home/centos/tmp/var/log/nginx/error.log

バージョンを確認します

.. code-block:: cmdin

  /home/centos/tmp/usr/sbin/nginx -v

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

  nginx version: nginx/1.23.2 (nginx-plus-r28)


インストール時に表示された内容を参考に、NGINXを実行します

.. code-block:: cmdin

  /home/centos/tmp/usr/sbin/nginx -p /home/centos/tmp/etc/nginx -c nginx.conf -e /home/centos/tmp/var/log/nginx/error.log

動作を確認します。特権権限ではないのでWellknownPortの利用でないことに注意してください

.. code-block:: cmdin

  curl -s localhost:8080 | grep title

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

  <title>Welcome to nginx!</title>

Tips3. NGINX Plus QUIC Package のインストール
====

R29 Quick Package のインストール
----

NGINX Plus R29 では、実験利用を目的とした NGINX Plus QUIC Packageを提供しています。

なお、NGINX Plus R30 以降はQUIC+HTTP/3のネイティブサポートとなりました。詳細は以下をご確認ください。

`NGINX Plus R30の発表 <https://www.f5.com/ja_jp/company/blog/nginx/nginx-plus-r30-released>`__

NGINX Plus QUICパッケージを利用する場合には、NGINX Plus に変わって NGINX Plus QUICをインストールします。
QUICを含まないNGINX Plusとの共存、NGINX Plus QUICパッケージでのNGINX AppProtect、NGINX AppProtect DoS は利用できませんのでご注意ください。

`1. NGINX Plusのインストール (15min)  <https://f5j-nginx-plus-lab1.readthedocs.io/en/latest/class1/module2/module2.html#nginx-plus-15min>`__
のインストール直前までの手順
`1. NGINX Licenseファイルのコピー <https://f5j-nginx-plus-lab1.readthedocs.io/en/latest/class1/module2/module2.html#nginx-license>`__
、
`2. コマンドの実行 <https://f5j-nginx-plus-lab1.readthedocs.io/en/latest/class1/module2/module2.html#id1>`__
の手順を実行してください。

NGINX Plus QUICを利用するため以下の手順を実施してください

インストールに必要となる手順を実施します

.. code-block:: cmdin

  # リポジトリに利用する鍵を取得します
  wget -qO - https://cs.nginx.com/static/keys/nginx_signing.key | gpg --dearmor | sudo tee /usr/share/keyrings/nginx-archive-keyring.gpg >/dev/null

  # NGINX Plus QUICのレポジトリ情報を追加します
  printf "deb [signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg] https://pkgs.nginx.com/plus-quic/ubuntu `lsb_release -cs` nginx-plus\n" | sudo tee /etc/apt/sources.list.d/nginx-plus.list 

NGINX Plus QUICパッケージをインストールします

.. code-block:: cmdin

  # NGINX Plus QUICパッケージをインストールします
  sudo apt update
  sudo apt install nginx-plus-quic

インストールされたバージョンを確認します

.. code-block:: cmdin

  nginx -v

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

  nginx version: nginx/1.23.4 (nginx-plus-quic-r29)

参考設定
----

以下にサンプルの手順、動作確認手順を示します。

設定ファイルを作成します

.. code-block:: cmdin

  vi ~/quic-sample.conf

以下の内容を反映し、設定を保存します

.. code-block:: cmdin

  log_format quic '$remote_addr - $remote_user [$time_local]'
  '"$request" $status $body_bytes_sent "$http_referer" "$http_user_agent" "$http3"';
  access_log /var/log/nginx/access.log quic;
      
  server {
  
      # for better compatibility we recommend
      # using the same port number for QUIC and TCP
      listen 443 quic reuseport; # QUIC
      listen 443 ssl;            # TCP
  
      # please specify cert and key that you use.
      ssl_certificate     conf.d/www.example.local.crt;
      ssl_certificate_key conf.d/www.example.local.key;
      ssl_protocols       TLSv1.3;
  
      location / {
          # advertise that QUIC is available on the configured port
          add_header Alt-Svc 'h3=":$server_port"; ma=86400';
          root   /usr/share/nginx/html;
          index  index.html index.htm;
          #proxy_pass <upstream_group>;
          #root       /<root_directory>;
      }
  }

証明書をと鍵を作成します

.. code-block:: cmdin

  openssl ecparam -out ~/www.example.local.key -name prime256v1 -genkey 
  openssl req -new -key ~/www.example.local.key -out ~/www.example.local-csr.pem -subj '/CN=www.example.local' 
  openssl req -x509 -nodes -days 30 -key ~/www.example.local.key -in ~/www.example.local-csr.pem -out ~/www.example.local.crt

ファイルをコピーします

.. code-block:: cmdin

  sudo cp ~/quic-sample.conf /etc/nginx/conf.d/
  sudo cp ~/www.example.local* /etc/nginx/conf.d/

設定を読み込みます

.. code-block:: cmdin

  sudo nginx -s reload 

動作確認
----

http3が有効なcurlコマンドを実行するため、Docker Imageを作成します。まずDockerfileを作成します

.. code-block:: cmdin

  vi Dockerfile

以下の内容を貼り付けて保存してください

.. code-block:: cmdin

  # syntax=docker/dockerfile:1
  FROM ubuntu:20.04

  RUN apt update && apt install -y git autoconf cmake libtool build-essential libssl-dev
  RUN git clone -b v0.6.0 --depth 1 --recursive https://github.com/nibanks/msh3 \
      && cd msh3 && mkdir build && cd build \
      && cmake -G 'Unix Makefiles' -DCMAKE_BUILD_TYPE=RelWithDebInfo .. \
      && cmake --build . \
      && cmake --install .

  RUN git clone https://github.com/curl/curl -b curl-8_0_1 \
      && cd curl \
      && autoreconf -fi \
      && ./configure LDFLAGS="-Wl,-rpath,/usr/local/lib" --with-msh3=/usr/local --with-openssl \
      && make \
      && make install


コンテナイメージをビルドします

.. code-block:: cmdin

  sudo docker build --no-cache -t curl-http3-msquic .

ビルドしたイメージを利用し、curlを実行します

.. code-block:: cmdin

  # sudo docker run -it curl-http3-msquic /bin/bash
  sudo docker run curl-http3-msquic curl -v -k -m 2 --resolve www.example.local:443:10.1.1.6 --http3 "https://www.example.local:443/"
