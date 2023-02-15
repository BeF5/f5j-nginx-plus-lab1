Tips1. NGINX Plus の Offline Install
====

NGINX Plus Subscriptionをご契約のお客様は、MyF5より各OSに対応したNGINX Plusや、Moduleをダウンロードすることが可能となります。
それらを利用し、OfflineのInstallが可能となります。

MyF5を利用せずファイルを取得する場合、`Tips2. Unprivilege(非特権)ユーザのInstall <#tips2-unprivilege-install>`__ の ``NGINX Plus Binaryの取得`` を参照してください。

1. Offline Install
----

パッケージのインストール

.. code-block:: cmdin

  # Ubuntu / Debian
  sudo dpkg -i  nginx-plus_28-1~focal_amd64.deb

  # RedHat / CentOS / Oracle Linux / Rocky Linux / Alma Linux
  rpm -ivh nginx-plus-28-1.el8.ngx.x86_64.rpm

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

