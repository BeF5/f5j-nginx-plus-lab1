
NGINX Plus の動作
=================

1. NGINX Plusのインストール (15min)
-----------------------------------

| 本ページに記載する手順に従ってNGINX Plus をインストールします
| 参考：\ `Installing NGINX Plus on Ubuntu <https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-plus/#installing-nginx-plus-on-ubuntu>`__

.. NOTE::
   手順確認の目的で、NGINX Plusの他、NGINX App Protect WAF、NGINX App
   Protect Dosのインストール手順も示しています。
   ただし、本ラボでセキュリティ機能の確認はありません

1. NGINX Licenseファイルのコピー
~~~~~~~~

| ライセンスファイルをコピーしてください
  ファイルがラボ環境に配置されていない場合、トライアルを申請し証明書と鍵を取得してください
| トライアルの申請方法は\ `トライアル申請方法 <http://f5j-nginx-plus-trial.readthedocs.io/>`__\ を参照してください

.. NOTE::
   取得したライセンスファイルを\ ``Jump Host``\ にコピーした後、\ ``ubuntu-01``\ に送信するために\ ``pscp``\ をご利用いただくことが可能です。以下コマンドを参考にご利用ください。コマンドプロンプト、powershellなどのターミナルから実行いただけます
   
   なお、ubuntu01/02およびdocker_hostにライセンスファイルを格納しております。格納位置は以下になります

   Windows2019_JumpBox、vnc-windows：C:\\Users\\user\\Desktop\\Key
   
   Ubuntu01/02：/home/ubuntu/
   
   docker_host：/home/centos/

   .. code-block:: bash

      コマンド: pscp -i <SSHで利用する公開鍵> <送付するファイル> <宛先>

      pscp -i .\.ssh\id_rsa-putty.ppk <送信するファイル> ubuntu@10.1.1.7:/home/ubuntu

.. code-block:: cmdin

   sudo mkdir -p /etc/ssl/nginx
   sudo cp ~/nginx-repo.crt /etc/ssl/nginx/
   sudo cp ~/nginx-repo.key /etc/ssl/nginx/

2. コマンドの実行
~~~~~~~~


NGINX、App Protect WAF と App Protect DoS
のリポジトリに利用する鍵を取得します

.. code-block:: cmdin

   sudo wget -qO - https://cs.nginx.com/static/keys/nginx_signing.key | sudo gpg --dearmor | sudo tee /usr/share/keyrings/nginx-archive-keyring.gpg >/dev/null

   sudo wget -qO - https://cs.nginx.com/static/keys/app-protect-security-updates.key | sudo gpg --dearmor | sudo tee /usr/share/keyrings/app-protect-security-updates.gpg >/dev/null

必要となるパッケージをインストールします

.. code-block:: cmdin
   
   sudo apt-get update

   sudo apt-get install apt-transport-https lsb-release ca-certificates wget gnupg2 ubuntu-keyring

レポジトリの情報を追加します

.. code-block:: cmdin

   # NGINX Plusのレポジトリ情報
   printf "deb [signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg] https://pkgs.nginx.com/plus/R32/ubuntu `lsb_release -cs` nginx-plus\n" | sudo tee /etc/apt/sources.list.d/nginx-plus.list

   # NGINX App Protectのレポジトリ情報
   printf "deb [signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg] https://pkgs.nginx.com/app-protect/ubuntu `lsb_release -cs` nginx-plus\n" | sudo tee /etc/apt/sources.list.d/nginx-app-protect.list

   printf "deb [signed-by=/usr/share/keyrings/app-protect-security-updates.gpg] https://pkgs.nginx.com/app-protect-security-updates/ubuntu `lsb_release -cs` nginx-plus\n" | sudo tee -a /etc/apt/sources.list.d/nginx-app-protect.list

   # NGINX App Protect DoSのレポジトリ情報
   printf "deb [signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg] https://pkgs.nginx.com/app-protect-dos/ubuntu `lsb_release -cs` nginx-plus\n" | sudo tee /etc/apt/sources.list.d/nginx-app-protect-dos.list

aptコマンドの設定情報を取得します

.. code-block:: cmdin

   sudo wget -P /etc/apt/apt.conf.d https://cs.nginx.com/static/files/90pkgs-nginx

パッケージ情報を更新します

.. code-block:: cmdin

   sudo apt-get update

3. NGINX パッケージのインストール
~~~~~~~~


.. code-block:: cmdin

   sudo apt-get install -y nginx-plus
   sudo apt-get install -y app-protect app-protect-attack-signatures
   sudo apt-get install -y app-protect-dos
   # NAP DoS Release 4.4 より
   sudo apt-get install -y app-protect-dos app-protect-dos-ebpf

インストールしたパッケージの情報の確認します

| 参考となる記事はこちらです。
| `K72015934: Display the NGINX software version <https://support.f5.com/csp/article/K72015934>`__

.. code-block:: cmdin

   nginx -v

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

  nginx version: nginx/1.23.4 (nginx-plus-r29)

``-V`` (大文字)　を指定することによりパッケージが利用するOpenSSLの情報や、configureのオプションを確認できます。

.. code-block:: cmdin

   nginx -V

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

  nginx version: nginx/1.23.4 (nginx-plus-r29)
  built by gcc 9.3.0 (Ubuntu 9.3.0-10ubuntu2)
  built with OpenSSL 1.1.1f  31 Mar 2020
  TLS SNI support enabled
  configure arguments: --prefix=/etc/nginx --sbin-path=/usr/sbin/nginx --modules-path=/usr/lib/nginx/modules --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --pid-path=/var/run/nginx.pid --lock-path=/var/run/nginx.lock --http-client-body-temp-path=/var/cache/nginx/client_temp --http-proxy-temp-path=/var/cache/nginx/proxy_temp --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp --http-scgi-temp-path=/var/cache/nginx/scgi_temp --user=nginx --group=nginx --with-compat --with-file-aio --with-threads --with-http_addition_module --with-http_auth_request_module --with-http_dav_module --with-http_flv_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_mp4_module --with-http_random_index_module --with-http_realip_module --with-http_secure_link_module --with-http_slice_module --with-http_ssl_module --with-http_stub_status_module --with-http_sub_module --with-http_v2_module --with-mail --with-mail_ssl_module --with-stream --with-stream_realip_module --with-stream_ssl_module --with-stream_ssl_preread_module --build=nginx-plus-r29 --with-http_auth_jwt_module --with-http_f4f_module --with-http_hls_module --with-http_proxy_protocol_vendor_module --with-http_session_log_module --with-stream_mqtt_filter_module --with-stream_mqtt_preread_module --with-stream_proxy_protocol_vendor_module --with-cc-opt='-g -O2 -fdebug-prefix-map=/data/builder/debuild/nginx-plus-1.23.4/debian/debuild-base/nginx-plus-1.23.4=. -fstack-protector-strong -Wformat -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2 -fPIC' --with-ld-opt='-Wl,-Bsymbolic-functions -Wl,-z,relro -Wl,-z,now -Wl,--as-needed -pie'

またUbuntuの環境では以下サンプルのようにパッケージの詳細を確認することが可能です。

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:
  :emphasize-lines: 2-3,8,6

  # sudo apt show nginx-plus
  Package: nginx-plus
  Version: 29-1~focal
  Priority: optional
  Section: httpd
  Maintainer: NGINX Packaging <nginx-packaging@f5.com>
  Installed-Size: 6760 kB
  Provides: httpd, nginx, nginx-plus-r29
  Depends: libc6 (>= 2.28), libcrypt1 (>= 1:4.1.0), libpcre2-8-0 (>= 10.22), libssl1.1 (>= 1.1.1), zlib1g (>= 1:1.1.4), lsb-base (>= 3.0-6), adduser
  Conflicts: nginx, nginx-common, nginx-core
  Replaces: nginx, nginx-core, nginx-plus-debug
  Homepage: https://www.nginx.com/
  Download-Size: 3369 kB
  APT-Manual-Installed: yes
  APT-Sources: https://pkgs.nginx.com/plus/ubuntu focal/nginx-plus amd64 Packages
  Description: NGINX Plus, provided by Nginx, Inc.
   NGINX Plus extends NGINX open source to create an enterprise-grade
   Application Delivery Controller, Accelerator and Web Server. Enhanced
   features include: Layer 4 and Layer 7 load balancing with health checks,
   session persistence and on-the-fly configuration; Improved content caching;
   Enhanced status and monitoring information; Streaming media delivery.

- ``2~3,8行目`` : 指定したNGINX Plusのパッケージであることが確認できます
- ``6行目`` : MaintainerとしてF5の情報が確認できます

NGINX App Protect のVersion

.. code-block:: cmdin

   cat /opt/app_protect/VERSION

NGINX App Protect DoS のVersion

.. code-block:: cmdin

   admd -v

その他インストールしたパッケージの情報を確認いただけます。ラボ環境のホストはUbuntuとなります。

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   # dpkg-query -l | grep nginx-plus
   ii  nginx-plus                         25-1~focal                            amd64        NGINX Plus, provided by Nginx, Inc.
   ii  nginx-plus-module-appprotect       25+3.671.0-1~focal                    amd64        NGINX Plus app protect dynamic module version 3.671.0
   ii  nginx-plus-module-appprotectdos    25+2.0.1-1~focal                      amd64        NGINX Plus appprotectdos dynamic module

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   # dpkg-query -l | grep app-protect
   ii  app-protect                        28+4.2.0-1~focal                      amd64        App-Protect package for Nginx Plus, Includes all of the default files and examples. Nginx App Protect provides web application firewall (WAF) security protection for your web applications, including OWASP Top 10 attacks.
   ii  app-protect-attack-signatures      2023.01.09-1~focal                    amd64        Attack Signature Updates for App-Protect
   ii  app-protect-common                 10.179.0-1~focal                      amd64        NGINX App Protect
   ii  app-protect-compiler               10.179.0-1~focal                      amd64        Control-plane(aka CP) for waf-general debian
   ii  app-protect-dos                    28+3.1.7-1~focal                      amd64        Nginx DoS protection
   ii  app-protect-dos-ebpf               28+3.1.7-1~focal                      amd64        Nginx DoS protection
   ii  app-protect-engine                 10.179.0-1~focal                      amd64        NGINX App Protect
   ii  app-protect-plugin                 4.2.0-1~focal                         amd64        NGINX App Protect plugin
   ii  app-protect-threat-campaigns       2023.01.11-1~focal                    amd64        Threat Campaign Updates for App-Protect

2. NGINXの基礎
--------------

1. ステータスの確認 (5min)
~~~~~~~~

NGINX Plusのアーキテクチャ

   - .. image:: ./media/nginx_architecture.jpg
       :width: 400

   - .. image:: ./media/nginx_architecture2.jpg
       :width: 400


NGINX の停止・起動

.. code-block:: cmdin

   sudo service nginx stop
   sudo service nginx start

NGINX のstatusを確認します

.. code-block:: cmdin

   sudo service nginx status

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   ● nginx.service - NGINX Plus - high performance web server
        Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
        Active: active (running) since Mon 2021-11-22 10:12:55 UTC; 11s ago
          Docs: https://www.nginx.com/resources/
       Process: 9126 ExecStartPre=/usr/lib/nginx-plus/check-subscription (code=exited, status=0/SUCCESS)
       Process: 9146 ExecStart=/usr/sbin/nginx -c /etc/nginx/nginx.conf (code=exited, status=0/SUCCESS)
      Main PID: 9147 (nginx)
         Tasks: 3 (limit: 2327)
        Memory: 2.6M
        CGroup: /system.slice/nginx.service
                ├─9147 nginx: master process /usr/sbin/nginx -c /etc/nginx/nginx.conf
                ├─9148 nginx: worker process
                └─9149 nginx: worker process

   Nov 22 10:12:55 ip-10-1-1-7 systemd[1]: Starting NGINX Plus - high performance web server...
   Nov 22 10:12:55 ip-10-1-1-7 systemd[1]: nginx.service: Can't open PID file /run/nginx.pid (yet?) after start: Operation not permitted
   Nov 22 10:12:55 ip-10-1-1-7 systemd[1]: Started NGINX Plus - high performance web server.

pidファイルの配置場所の確認します

.. code-block:: cmdin

   grep pid /etc/nginx/nginx.conf

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   pid        /var/run/nginx.pid;

pidの内容を確認します

.. code-block:: cmdin

   cat /var/run/nginx.pid

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   9147

論理コア数を確認します

.. code-block:: cmdin

   grep processor /proc/cpuinfo | wc -l

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   2

NGINX Processを確認します

NGINXはMaster Processと通信制御を行うWorker Processに分かます。Worker ProcessはCPUCore数の数起動し、並列処理を行う設定となっている。 Master ProcessのPIDがPIDファイルに記載されている内容と一致していることを確認する
また、Worker ProcessがCPU Core数の数だけ起動していることを確認します

.. code-block:: cmdin

   ps aux | grep nginx

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   nginx       9122  0.0  0.0   2616   608 ?        Ss   10:12   0:00 /bin/sh -c usr/share/ts/bin/bd-socket-plugin tmm_count 4 proc_cpuinfo_cpu_mhz 2000000 total_xml_memory 307200000 total_umu_max_size 3129344 sys_max_account_id 1024 no_static_config 2>&1 >> /var/log/app_protect/bd-socket-plugin.log
   nginx       9123  0.3  3.0 385260 61592 ?        Sl   10:12   0:00 usr/share/ts/bin/bd-socket-plugin tmm_count 4 proc_cpuinfo_cpu_mhz 2000000 total_xml_memory 307200000 total_umu_max_size 3129344 sys_max_account_id 1024 no_static_config
   nginx       9125  0.0  0.0   2616   608 ?        Ss   10:12   0:00 /bin/sh -c /usr/bin/admd -d --log info 2>&1 > /var/log/adm/admd.log
   nginx       9127  0.5  2.5 799208 50732 ?        Sl   10:12   0:00 /usr/bin/admd -d --log info
   root        9147  0.0  0.0   9136   892 ?        Ss   10:12   0:00 nginx: master process /usr/sbin/nginx -c /etc/nginx/nginx.conf
   nginx       9148  0.0  0.1   9764  3528 ?        S    10:12   0:00 nginx: worker process
   nginx       9149  0.0  0.1   9764  3528 ?        S    10:12   0:00 nginx: worker process


2. Directive / Block (5min)
~~~~~~~~

   - .. image:: ./media/nginx_directive.jpg
       :width: 400


3. Configの階層構造 (5min)
~~~~~~~~

   - .. image:: ./media/nginx_directive2.jpg
       :width: 400

   - .. image:: ./media/nginx_directive3.jpg
       :width: 400

   - .. image:: ./media/nginx_directive4.jpg
       :width: 400

3. 基本的な動作の確認
---------------------


1.  事前ファイルの取得 (5min)
~~~~~~~~

ラボで必要なファイルをGitHubから取得します

.. code-block:: cmdin

   sudo su - 
   cd ~/
   git clone https://github.com/BeF5/f5j-nginx-plus-lab1-conf.git


2.  設定のテスト、設定の反映 (10min)
~~~~~~~~

ディレクトリを移動し、必要なファイルをコピーします

.. code-block:: cmdin

   cp ~/f5j-nginx-plus-lab1-conf/lab/incomplete.conf /etc/nginx/conf.d/default.conf

設定ファイルの内容を確認します

.. code-block:: cmdin

   cat ~/f5j-nginx-plus-lab1-conf/lab/incomplete.conf

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   server {
       # you need to add ; at end of listen directive.
       listen       81
       server_name  localhost;
       location / {
           root   /usr/share/nginx/html;
           index  index.html index.htm;
       }
   }

基本的なコマンドと、Signalについて以下を確認してください。 

   - .. image:: ./media/nginx_command.jpg
       :width: 400

   - .. image:: ./media/nginx_command2.jpg
       :width: 400


| NGINX Config Fileを反映する前にテストすることが可能です。コマンドを実行し、テスト結果を確認してください。
| ``-t`` と ``-T`` の2つのオプションを実行し、違いを確認します。

まず、オプションの内容を確認してください。

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   # nginx -h
   nginx version: nginx/1.21.3 (nginx-plus-r25)
   Usage: nginx [-?hvVtTq] [-s signal] [-p prefix]
                [-e filename] [-c filename] [-g directives]

   Options:
     -?,-h         : this help
     -v            : show version and exit
     -V            : show version and configure options then exit
     -t            : test configuration and exit
     -T            : test configuration, dump it and exit
     -q            : suppress non-error messages during configuration testing
     -s signal     : send signal to a master process: stop, quit, reopen, reload
     -p prefix     : set prefix path (default: /etc/nginx/)
     -e filename   : set error log file (default: /var/log/nginx/error.log)
     -c filename   : set configuration file (default: /etc/nginx/nginx.conf)
     -g directives : set global directives out of configuration file

テストを実行します(\ ``-t``)

.. code-block:: cmdin

   nginx -t


.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   nginx: [emerg] invalid parameter "server_name" in /etc/nginx/conf.d/default.conf:4
   nginx: configuration file /etc/nginx/nginx.conf test failed

| “server_name” directive でエラーとなっていることがわかります。
  これは、その一つ前の行が正しく「；(セミコロン)」で終わっていないことが問題となります。
| エディタで設定ファイルを開き修正してください

.. code-block:: cmdin

   vi /etc/nginx/conf.d/default.conf


.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   listen directiveの文末に ; を追加してください。
   ---
   [変更前]    listen       81
   [変更後]    listen       81;
   ---

| 再度テストを実行してください。
| ``-t`` の実行

.. code-block:: cmdin

   nginx -t

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
   nginx: configuration file /etc/nginx/nginx.conf test is successful

``-T`` の実行

.. code-block:: cmdin

   nginx -T

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
   nginx: configuration file /etc/nginx/nginx.conf test is successful
   # configuration file /etc/nginx/nginx.conf:

   user  nginx;
   worker_processes  auto;

   error_log  /var/log/nginx/error.log notice;
   pid        /var/run/nginx.pid;


   events {
       worker_connections  1024;
   }


   http {
       include       /etc/nginx/mime.types;
       default_type  application/octet-stream;

       log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                         '$status $body_bytes_sent "$http_referer" '
                         '"$http_user_agent" "$http_x_forwarded_for"';

       access_log  /var/log/nginx/access.log  main;

       sendfile        on;
       #tcp_nopush     on;

       keepalive_timeout  65;

       #gzip  on;

       include /etc/nginx/conf.d/*.conf;
   }


   ※省略※
   # configuration file /etc/nginx/conf.d/default.conf:
   server {
       # you need to add ; at end of listen directive.
       listen       81;
       server_name  localhost;
       location / {
           root   /usr/share/nginx/html;
           index  index.html index.htm;
       }
   }

| 設定の読み込み、動作確認をします。
| 正しく Port 81 でListenしていることを確認してください

.. code-block:: cmdin

   nginx -s reload
   ss -anp | grep nginx | grep LISTEN


.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   tcp    LISTEN  0       511                                              0.0.0.0:81                                                0.0.0.0:*                      users:(("nginx",pid=9341,fd=12),("nginx",pid=9340,fd=12),("nginx",pid=9147,fd=12))

curlコマンドを実行します

.. code-block:: cmdin

   curl -s localhost:81 | grep title

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   <title>Welcome to nginx!</title>

3.  設定の継承 (10min)
~~~~~~~~

ラボで使用するファイルをコピーします

.. code-block:: cmdin

  cp -r ~/f5j-nginx-plus-lab1-conf/html /etc/nginx/conf.d
  cp ~/f5j-nginx-plus-lab1-conf/lab/inheritance.conf /etc/nginx/conf.d/default.conf

| 設定ファイルの確認してください。
| 本設定では、indexがポイントとなります。

listen 80では、indexを個別に記述をしていません。 listen 8080では、
indexとして main.html を指定しています。 また、それぞれ root の記述方法が異なっています。

.. code-block:: cmdin

  cat ~/f5j-nginx-plus-lab1-conf/lab/inheritance.conf

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   index index.html;
   server {
           listen 80;
           root conf.d/html;
   }
   server {
           listen 8080;
           root /etc/nginx/conf.d/html;
           index main.html;
   }

設定を反映し、これらがどのように動作するのか見てみましょう

.. code-block:: cmdin

   nginx -s reload
   ss -anp | grep nginx | grep LISTEN

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   tcp    LISTEN  0       511                                              0.0.0.0:8080                                              0.0.0.0:*                      users:(("nginx",pid=9392,fd=9),("nginx",pid=9391,fd=9),("nginx",pid=9147,fd=9))
   tcp    LISTEN  0       511                                              0.0.0.0:80                                                0.0.0.0:*                      users:(("nginx",pid=9392,fd=8),("nginx",pid=9391,fd=8),("nginx",pid=9147,fd=8))

Port 80 に対し、curlコマンドを実行します

.. code-block:: cmdin

   curl -s localhost:80 | grep path

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

       <h2>path: html/index.html</h2>     

Port 8080 に対し、curlコマンドを実行します

.. code-block:: cmdin

   curl -s localhost:8080 | grep path


.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

       <h2>path: html/main.html</h2>

4.  server directive (10min)
~~~~~~~~

NGINXが通信を待ち受ける動作について以下を確認してください

   - .. image:: ./media/nginx_server.jpg
       :width: 400

   - .. image:: ./media/nginx_server2.jpg
       :width: 400

ラボで使用するファイルをコピーします

.. code-block:: cmdin

   cp ~/f5j-nginx-plus-lab1-conf/lab/blank-defaultbehavior.conf /etc/nginx/conf.d/default.conf

設定内容を確認します

.. code-block:: cmdin

   cat ~/f5j-nginx-plus-lab1-conf/lab/blank-defaultbehavior.conf

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   server {

   }

設定を反映します

.. code-block:: cmdin

   nginx -s reload
   ss -anp | grep nginx | grep LISTEN


.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   tcp    LISTEN  0       511                                              0.0.0.0:80                                                0.0.0.0:*                      users:(("nginx",pid=9445,fd=8),("nginx",pid=9444,fd=8),("nginx",pid=9147,fd=8))

| 設定が反映され、80でListenしていることが確認できます。
| curlコマンドで結果を確認します

.. code-block:: cmdin

   curl localhost:80


.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   <html>
   <head><title>404 Not Found</title></head>
   <body>
   <center><h1>404 Not Found</h1></center>
   <hr><center>nginx/1.21.3</center>
   </body>

| 404エラーとなりました。これはどこを参照しているのでしょうか。
| 各directiveのdefaultパラメータを確認してください

| `nginx.org : root
  directive <http://nginx.org/en/docs/http/ngx_http_core_module.html#root>`__
| `nginx.org : index
  directive <http://nginx.org/en/docs/http/ngx_http_index_module.html#index>`__
| `nginx.org : listen
  directive <http://nginx.org/en/docs/http/ngx_http_core_module.html#listen>`__

これらの内容より、server
directiveに設定を記述しない場合にも、defaultのパラメータで動作していることが確認できます。

それでは対象となるディレクトリにファイルをコピーします

.. code-block:: cmdin

  mkdir /etc/nginx/html
  cp /etc/nginx/conf.d/html/default-path_index.html /etc/nginx/html/index.html


| htmlファイルを配置しました。
| 設定ファイルに変更は加えておりませんので、再度curlコマンドで結果を確認します

.. code-block:: cmdin

   curl -s localhost:80 | grep default

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

       <h2>This is default html file path</h2>

今度は正しく結果が表示されました。
このようにdefaultパラメータの動作を確認できました

5.  listen directive (10min)
~~~~~~~~

| listen directiveを利用することにより、NGINXが待ち受けるIPアドレスやポート番号など指定することができます。
| 以下のような記述で意図した動作となるよう設定をします 

   - .. image:: ./media/nginx_listen.jpg
       :width: 400

   - .. image:: ./media/nginx_listen2.jpg
       :width: 400


ラボで使用するファイルをコピーします

.. code-block:: cmdin

   cp ~/f5j-nginx-plus-lab1-conf/lab/multi-listen.conf /etc/nginx/conf.d/default.conf

設定内容を確認し、反映します

.. code-block:: cmdin

   cat ~/f5j-nginx-plus-lab1-conf/lab/multi-listen.conf


.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   # server {
   #    ## no listen directive
   # }

   server {
       listen 127.0.0.1:8080;
   }

   server {
       listen 127.0.0.2;
   }

   server {
       listen 8081;
   }

   server {
       listen unix:/var/run/nginx.sock;
   }

設定を反映します

.. code-block:: cmdin

   service nginx restart

| 設定で指定したポート番号やソケットでListenしていることを確認してください。
| （正しく設定が読み込めない場合は、再度上記コマンドにて設定を読み込んでください)

ソケットが生成されていることを確認します

.. code-block:: cmdin

   ls /var/run/nginx.sock


.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   /var/run/nginx.sock

NGINXでListenしている内容を確認します

.. code-block:: cmdin

   ss -anp | grep nginx | grep LISTEN

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   u_str LISTEN    0      511                                  /var/run/nginx.sock 60394                                                   * 0                      users:(("nginx",pid=9947,fd=9),("nginx",pid=9946,fd=9),("nginx",pid=9945,fd=9))
   tcp   LISTEN    0      511                                            127.0.0.2:80                                                0.0.0.0:*                      users:(("nginx",pid=9947,fd=7),("nginx",pid=9946,fd=7),("nginx",pid=9945,fd=7))
   tcp   LISTEN    0      511                                            127.0.0.1:8080                                              0.0.0.0:*                      users:(("nginx",pid=9947,fd=6),("nginx",pid=9946,fd=6),("nginx",pid=9945,fd=6))
   tcp   LISTEN    0      511                                              0.0.0.0:8081                                              0.0.0.0:*                      users:(("nginx",pid=9947,fd=8),("nginx",pid=9946,fd=8),("nginx",pid=9945,fd=8))

それぞれ Listen している内容に対して接続できることを確認してください

.. code-block:: cmdin

   curl -s 127.0.0.1:8080 | grep default

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

       <h2>This is default html file path</h2>

.. code-block:: cmdin

   curl -s 127.0.0.2:80 | grep default

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

       <h2>This is default html file path</h2>

.. code-block:: cmdin

   curl -s 127.0.0.1:8081 | grep default

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

       <h2>This is default html file path</h2>

.. code-block:: cmdin

   curl -s --unix-socket /var/run/nginx.sock http: | grep default

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

       <h2>This is default html file path</h2>

socketを削除し、NGINXが起動することを確認します

.. code-block:: cmdin

  rm /var/run/nginx.sock
  rm /etc/nginx/conf.d/default.conf
  service nginx restart

6.  server_name directive (10min)
~~~~~~~~

server_name directiveを利用することにより、待ち受けるFQDNを指定することが可能です。

ラボで使用するファイルをコピーします

.. code-block:: cmdin

   cp ~/f5j-nginx-plus-lab1-conf/lab/multi-server_name.conf /etc/nginx/conf.d/default.conf

設定内容を確認し、反映します

.. code-block:: cmdin

   cat ~/f5j-nginx-plus-lab1-conf/lab/multi-server_name.conf 

実行結果を確認します

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   server {
       server_name example.com;
       return 200 "example.com\n";
   }

   server {
       server_name host1.example.com;
       return 200 "host1.example.com\n";
   }

   server {
           server_name www.example.*;
       return 200 "www.example.*\n";
   }
   server{
           server_name *.org;
       return 200 "*.org\n";
   }
   server {
           server_name *.example.org;
       return 200 "*.example.org\n";
   }

   server {
           listen 80;
           server_name ~^(www2|host2).*\.example\.com$;
      return 200 "~^(www2|host2).*\.example\.com\n";
   }
   server {
           listen 80;
           server_name ~^.*\.example\..*$;
       return 200 "~^.*\.example\..*\n";
   }
   server {
           listen 80;
           server_name ~^(host2|host3).*\.example\.com$;
       return 200 "~^(host2|host3).*\.example\.com\n";
   }

設定を反映します

.. code-block:: cmdin

   nginx -s reload

server_nameの処理順序は以下です

   .. image:: ./media/nginx_server_name.jpg
       :width: 400

以下のコマンドを実行し結果を確認します。
どのような処理が行われているか確認してください

完全一致する結果を確認します

.. code-block:: cmdin

   curl localhost -H 'Host:host1.example.com'

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   host1.example.com

Wild Cardの前方一致する結果を確認します

.. code-block:: cmdin

   curl localhost -H 'Host:www.example.co.jp'

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   www.example.*

正規表現のはじめに一致する結果を確認します

.. code-block:: cmdin
   
   curl localhost -H 'Host:host2.example.co.jp'

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   ~^.*\.example\..*

.. code-block:: cmdin
   
   curl localhost -H 'Host:host2.example.com'

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   ~^(www2|host2).*\.example\.com

7.  location directive (10min)
~~~~~~~~

ラボで使用するファイルをコピーします

.. code-block:: cmdin

   cp ~/f5j-nginx-plus-lab1-conf/lab/multi-location.conf /etc/nginx/conf.d/default.conf

設定内容を確認し、反映します

.. code-block:: cmdin

   cat ~/f5j-nginx-plus-lab1-conf/lab/multi-location.conf

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   server {
      listen 80;
      location / {
         return 200 "LOCATION: / , URI: $request_uri, PORT: $server_port\n";
      }
      location ~* \.(php|html)$ {
         return 200 "LOCATION: ~* \.(php|html), URI: $request_uri, PORT: $server_port\n";
      }
      location ^~ /app1 {
         return 200 "LOCATION: ^~ /app1, URI: $request_uri, PORT: $server_port\n";
      }
      location ~* /app1/.*\.(php|html)$ {
         return 200 "LOCATION: ~* /app1/.*\.(php|html), URI: $request_uri, PORT: $server_port\n";
      }
      location = /app1/index.php {
              return 200 "LOCATION: = /app1/index.php, URI: $request_uri, PORT: $server_port\n";
      }
      location  /app2 {
         return 200 "LOCATION: /app2, URI: $request_uri, PORT: $server_port\n";
      }
      location ~* /app2/.*\.(php|html)$ {
         return 200 "LOCATION: ~* /app2/.*\.(php|html), URI: $request_uri, PORT: $server_port\n";
      }

   }

設定を反映します。

.. code-block:: cmdin

   nginx -s reload

locationの処理順序は以下となります。

   .. image:: ./media/nginx_location.jpg
       :width: 400


期待した結果となることを確認してください

前方一致する結果を確認

.. code-block:: cmdin

   curl http://localhost/app1/index.html

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   LOCATION: ^~ /app1, URI: /app1/index.html, PORT: 80

正規表現で一致する結果を確認

.. code-block:: cmdin

   curl http://localhost/app2/index.html

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   LOCATION: ~* \.(php|html), URI: /app2/index.html, PORT: 80

8.  Proxy (5min)
~~~~~~~~

   - .. image:: ./media/nginx_proxy.jpg
       :width: 400

   - .. image:: ./media/nginx_proxy2.jpg
       :width: 400

   - .. image:: ./media/nginx_proxy3.jpg
       :width: 400


ラボで使用するファイルをコピーします

.. code-block:: cmdin

   cp ~/f5j-nginx-plus-lab1-conf/lab/proxy.conf /etc/nginx/conf.d/default.conf

設定内容を確認し、反映します

.. code-block:: cmdin

   cat ~/f5j-nginx-plus-lab1-conf/lab/proxy.conf

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   server {
       listen 80;
       location /app1 {
           proxy_pass http://backend1:81/otherapp;
       }
       location /app2 {
           proxy_pass http://backend1:81;
       }

   }

設定を反映します

.. code-block:: cmdin

   nginx -s reload

以下のコマンドを実行し結果を確認します。
どのような処理が行われているか確認してください。

.. code-block:: cmdin

   curl -s localhost/app1/usr1/index.php | jq .

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   {
     "request_uri": "/otherapp/usr1/index.php",
     "server_addr": "10.1.1.8",
     "server_port": "81"
   }

.. code-block:: cmdin

   curl -s localhost/app2/usr1/index.php | jq .

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   {
     "request_uri": "/app2/usr1/index.php",
     "server_addr": "10.1.1.8",
     "server_port": "81"
   }

9. Load Balancing (5min)
~~~~~~~~

   .. image:: ./media/nginx_lb.jpg
       :width: 400


ラボで使用するファイルをコピーします

.. code-block:: cmdin

  cp ~/f5j-nginx-plus-lab1-conf/lab/lb-weight.conf /etc/nginx/conf.d/default.conf
  cp ~/f5j-nginx-plus-lab1-conf/lab/lb-weight_plus_api.conf /etc/nginx/conf.d/plus_api.conf

設定内容を確認し、反映します

.. code-block:: cmdin

  cat ~/f5j-nginx-plus-lab1-conf/lab/lb-weight.conf

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   upstream server_group {
       zone backend 64k;
       server backend1:81 weight=1;
       server backend2:82 weight=2;
   }
   server {
       listen 80;
       location / {
           proxy_pass http://server_group;
       }
   }

.. NOTE::
   API、APIを活用したDashboardの機能は ``NGINX Plus`` の機能となります。 ``NGINX OSS`` では利用できません。

.. code-block:: cmdin

  cat ~/f5j-nginx-plus-lab1-conf/lab/lb-weight_plus_api.conf

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   server {
       listen 8888;
       access_log /var/log/nginx/mng_access.log;

       location /api {
           api write=on;
           # directives limiting access to the API
       }

       location = /dashboard.html {
           root   /usr/share/nginx/html;
       }

   }

設定を反映します

.. code-block:: cmdin

   nginx -s reload

作業を行うホストからブラウザでNGINX Plus Dashboardを開く場合、 ``ubuntu01``の接続はメニューより ``PLUS  DASHBOARD``をクリックしてください。
踏み台ホストから接続する場合、ブラウザで `http://10.1.1.7:8888/dashboard.html <http://10.1.1.7:8888/dashboard.html>`__ を開いてください

   .. image:: ./media/nginx_lb2.png
       :width: 400

以下コマンドを実行し、適切に分散されることを確認します。

.. code-block:: cmdin

   for i in {1..9}; do echo "==$i==" ; curl -s localhost | jq . ; sleep 1 ; done

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   ==1==
   {
     "request_uri": "/",
     "server_addr": "10.1.1.8",
     "server_port": "82"
   }
   ※省略※
   ==9==
   {
     "request_uri": "/",
     "server_addr": "10.1.1.8",
     "server_port": "82"
   }

Dashboardの結果が適切なweightで分散されていることを確認してください。

10.  トラフィックの暗号化 (5min)
~~~~~~~~

   .. image:: ./media/nginx_ssl.jpg
       :width: 400

ラボで使用するファイルをコピーします

.. code-block:: cmdin

  cp -r ~/f5j-nginx-plus-lab1-conf/ssl /etc/nginx/conf.d
  cp ~/f5j-nginx-plus-lab1-conf/lab/ssl.conf /etc/nginx/conf.d/default.conf

設定内容を確認し、反映します

.. code-block:: cmdin

  cat ~/f5j-nginx-plus-lab1-conf/lab/ssl.conf

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   server {
       listen 80;
           listen 443 ssl;
           ssl_certificate_key conf.d/ssl/nginx-ecc-p256.key;
           ssl_certificate conf.d/ssl/nginx-ecc-p256.pem;
           location / {
                   proxy_pass http://backend1:81;
           }
   }

設定を反映します

.. code-block:: cmdin

   nginx -s reload

以下のコマンドを実行し結果を確認します。

HTTPでのアクセスを確認

.. code-block:: cmdin

   curl -v http://localhost

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   *   Trying 127.0.0.1:80...
   * TCP_NODELAY set
   * Connected to localhost (127.0.0.1) port 80 (#0)
   > GET / HTTP/1.1
   > Host: localhost
   > User-Agent: curl/7.68.0
   > Accept: */*
   >
   * Mark bundle as not supporting multiuse
   < HTTP/1.1 200 OK
   < Server: nginx/1.21.3
   < Date: Mon, 22 Nov 2021 15:05:35 GMT
   < Content-Type: application/octet-stream
   < Content-Length: 65
   < Connection: keep-alive
   <
   * Connection #0 to host localhost left intact
   { "request_uri": "/","server_addr":"10.1.1.8","server_port":"81"}

HTTPSでのアクセスを確認

.. code-block:: cmdin

   curl -kv https://localhost

.. code-block:: bash
  :caption: 実行結果サンプル
  :linenos:

   *   Trying 127.0.0.1:443...
   * TCP_NODELAY set
   * Connected to localhost (127.0.0.1) port 443 (#0)
   * ALPN, offering h2
   * ALPN, offering http/1.1
   * successfully set certificate verify locations:
   *   CAfile: /etc/ssl/certs/ca-certificates.crt
     CApath: /etc/ssl/certs
   * TLSv1.3 (OUT), TLS handshake, Client hello (1):
   * TLSv1.3 (IN), TLS handshake, Server hello (2):
   * TLSv1.2 (IN), TLS handshake, Certificate (11):
   * TLSv1.2 (IN), TLS handshake, Server key exchange (12):
   * TLSv1.2 (IN), TLS handshake, Server finished (14):
   * TLSv1.2 (OUT), TLS handshake, Client key exchange (16):
   * TLSv1.2 (OUT), TLS change cipher, Change cipher spec (1):
   * TLSv1.2 (OUT), TLS handshake, Finished (20):
   * TLSv1.2 (IN), TLS handshake, Finished (20):
   * SSL connection using TLSv1.2 / ECDHE-ECDSA-AES256-GCM-SHA384
   * ALPN, server accepted to use http/1.1
   * Server certificate:
   *  subject: CN=localhost
   *  start date: Mar 24 01:04:24 2021 GMT
   *  expire date: Apr 23 01:04:24 2021 GMT
   *  issuer: CN=localhost
   *  SSL certificate verify result: self signed certificate (18), continuing anyway.
   > GET / HTTP/1.1
   > Host: localhost
   > User-Agent: curl/7.68.0
   > Accept: */*
   >
   * Mark bundle as not supporting multiuse
   < HTTP/1.1 200 OK
   < Server: nginx/1.21.3
   < Date: Mon, 22 Nov 2021 15:05:49 GMT
   < Content-Type: application/octet-stream
   < Content-Length: 65
   < Connection: keep-alive
   <
   * Connection #0 to host localhost left intact
   { "request_uri": "/","server_addr":"10.1.1.8","server_port":"81"}

