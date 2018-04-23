# SSL证书请求文件(CSR)生成指南

## 1. 生成私钥 Generate the private key
请使用以下命令来生成私钥 Please type the following command at the prompt:

openssl genrsa -des3 -out www.mydomain.com.key 2048

此命令将生成2048位的RSA私钥，私钥文件名为： www.mydomain.com.key，会提示您设定私钥密码，请设置密码，并牢记！

## 2. 生成CSR文件 Generate the CSR

请使用以下命令来生成CSR :

openssl req -new -key www.mydomain.com.key -out www.mydomain.com.csr

如果提示“Unable to load config info from /usr/local/ssl/openssl.cnf ”，则加上一个指定openssl.cnf 路径的参数：

openssl req -new -config openssl.cnf -key www.mydomain.com.key -out www.mydomain.com.csr

此命令将提示您输入X.509证书所要求的字段信息，包括国家(中国添CN)、省份、所在城市、单位名称、单位部门名称(可以不填直接回车)。请注意：除国家缩写必须填CN外，其余都可以是英文或中文。



您也可以直接使用一个命令同时生成私钥和CSR文件：

openssl req -newkey rsa:2048 -keyout yourname.key –out yourname.csr

或

openssl req -new -config openssl.cnf -nodes -keyout myserver.key -out myserver.csr

## 3. 备份私钥文件 Backup your private key


## csr测试工具

https://www.wosign.com/support/check_csr.htm


## 去除私钥的密码
openssl rsa -in www.mydomain.com.key -out nopass_www.mydomain.com.key

## DHE 参数生成
openssl dhparam -out dhparam.pem 4096


## 自签名证书
openssl x509 -req -in appv2.bjyixiu.com.csr -out appv2.bjyixiu.com.crt -signkey appv2.bjyixiu.com.key -days 3650

# 配置范例

	server {
	  listen [::]:443 default_server;
	  ssl on;
	  ssl_certificate_key /etc/ssl/cert/raymii_org.pem;
	  ssl_certificate /etc/ssl/cert/ca-bundle.pem;
	  ssl_ciphers 'AES128+EECDH:AES128+EDH:!aNULL';
	  ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
	  ssl_session_cache shared:SSL:10m;
	  ssl_stapling on;
	  ssl_stapling_verify on;
	  resolver 1.1.1.1 1.0.0.1 valid=300s;
	  resolver_timeout 10s;
	  ssl_prefer_server_ciphers on;
	  ssl_dhparam /etc/ssl/certs/dhparam.pem;
	  add_header Strict-Transport-Security max-age=63072000;
	  add_header X-Frame-Options DENY;
	  add_header X-Content-Type-Options nosniff;
	  root /var/www/;
	  index index.html index.htm;
	  server_name raymii.org;
	}



部分内容来自：
https://linux.cn/article-5374-1.html




# 使用 OpenSSL 生成自签名证书

OpenSSL 是 SSL 和 TLS 协议的开放式源代码实现。它在标准通信层上提供了加密传输层，允许其与诸多网络应用程序和服务相结合。Cloud Management Console 中的缺省 SSL 概要文件具有通用公共名称。将 SSL 概要文件关联至网关集群时，如果使用缺省 SSL 概要文件，那么发出 API 调用的应用程序可能无法根据提供的证书验证其所连接到的主机名。在这种情况下，您可以生成一个新的自签名证书， 以表示应用程序可以验证的公共名称。该主题告诉您如何使用 OpenSSL 工具箱生成自签名 SSL 证书，以启用 HTTPS 连接。

过程
要使用 OpenSSL 生成自签名 SSL 证书，请完成以下步骤：

写下您的 SSL 证书的公共名称 (CN)。 该公共名称 (CN) 是使用该证书的系统的标准名称。 如果您使用的是动态 DNS，那么 CN 应该具有通配符，例如： *.api.com. 否则，使用网关集群中设置的主机名或 IP 地址（例如，192.16.183.131 或 dp1.acme.com）。
运行以下 OpenSSL 命令来生成您的专用密钥和公用证书。回答问题并在出现提示时输入公共名称。
openssl req -newkey rsa:2048 -nodes -keyout key.pem -x509 -days 365 -out certificate.pemcopy to clipboard
检查已创建的证书：
openssl x509 -text -noout -in certificate.pemcopy to clipboard
将密钥和证书组合在 PKCS#12 (P12) 捆绑软件中：
 openssl pkcs12 -inkey key.pem -in certificate.pem -export -out certificate.p12copy to clipboard
验证您的 P12 文件。
openssl pkcs12 -in certificate.p12 -noout -infocopy to clipboard
在 Cloud Management Console 的导航部分中，单击 SSL 概要文件图标 “SSL 概要文件”图标。 这样会打开“SSL 概要文件”页面。
在“SSL 概要文件”页面中，单击添加 SSL 概要文件。
在名称文本字段中，输入新 SSL 概要文件的名称。
在“现有证书”部分中，单击选择文件，然后浏览以查找 certificate.p12，并选择该文件。
注：
P12 文件必须包含专用密钥、来自认证中心的公用证书，以及用于签名的所有中间证书。
P12 文件最多可包含 10 个中间证书。
在密码文本字段中，输入证书文件的密码。
注：
现有证书必须用密码加以保护。
单击上载证书。 这样会填充该证书。
要验证证书，请将针对信任库中提供的 CA，请求并验证证书滑块滑至打开位置。
在信任库窗口部分中，单击选择文件，然后浏览以查找“信任库”证书。
在密码文本字段中，输入证书文件的密码。
单击上载证书。 这样会填充该证书。
展开“协议”部分，以显示 SSL 和 TLS 版本。
使用复选框来指示 SSL 或 TLS 版本。
单击保存。 这样会上载证书并保存 SSL 或 TLS 版本。
在 Cloud Management Console 的导航部分中，单击集群图标（“集群”图标）。
在“网关集群”窗口区域中，单击设置。 这样会显示“网关集群”窗口。
在“网关集群”窗口中，单击选择文件，然后浏览以查找要与网关集群关联的 SSL概要文件。
单击保存。
结果
已实施您的 SSL 活动。


https://www.ibm.com/support/knowledgecenter/zh/SSWHYP_4.0.0/com.ibm.apimgmt.cmc.doc/task_apionprem_gernerate_self_signed_openSSL.html



生成认证中心的 PKCS#12 文件。
https://www.ibm.com/support/knowledgecenter/zh/SSWHYP_4.0.0/com.ibm.apimgmt.cmc.doc/task_apionprem_generate_pkcs_certificate.html
