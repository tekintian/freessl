# SSL证书,证书请求文件(CSR)生成指南

## 1. 生成私钥 Generate the private key
请使用以下命令来生成私钥 Please type the following command at the prompt:
```shell
openssl genrsa -des3 -out www.mydomain.com.key 2048
```
此命令将生成2048位的RSA私钥，私钥文件名为： www.mydomain.com.key，会提示您设定私钥密码，请设置密码，并牢记！

##2. 生成CSR文件 Generate the CSR

请使用以下命令来生成CSR :
```shell
openssl req -new -key www.mydomain.com.key -out www.mydomain.com.csr
```
如果提示“Unable to load config info from /usr/local/ssl/openssl.cnf ”，则加上一个指定openssl.cnf 路径的参数：
```shell
openssl req -new -config openssl.cnf -key www.mydomain.com.key -out www.mydomain.com.csr
```
此命令将提示您输入X.509证书所要求的字段信息，包括国家(中国添CN)、省份、所在城市、单位名称、单位部门名称(可以不填直接回车)。请注意：除国家缩写必须填CN外，其余都可以是英文或中文。



您也可以直接使用一个命令同时生成私钥和CSR文件：
```shell
openssl req -newkey rsa:2048 -keyout yourname.key –out yourname.csr
```
或
```shell
openssl req -new -config openssl.cnf -nodes -keyout myserver.key -out myserver.csr
```
## 3. 备份私钥文件 Backup your private key


### 去除私钥的密码
```shell
openssl rsa -in appv2.bjyixiu.com.key -out nopass_appv2.bjyixiu.com.key
```

###  自签名证书
```shell
openssl x509 -req -in appv2.bjyixiu.com.csr -out appv2.bjyixiu.com.crt -signkey appv2.bjyixiu.com.key -days 3650
```


## 使用 OpenSSL 生成自签名证书

### 要使用 OpenSSL 生成自签名 SSL 证书，请完成以下步骤：

	写下您的 SSL 证书的公共名称 (CN)。 该公共名称 (CN) 是使用该证书的系统的标准名称。 如果您使用的是动态 DNS，那么 CN 应该具有通配符，例如： *.api.com. 否则，使用网关集群中设置的主机名或 IP 地址（例如，192.16.183.131 或 dp1.acme.com）。

	运行以下 OpenSSL 命令来生成您的专用密钥和公用证书。回答问题并在出现提示时输入公共名称。
```shell
openssl req -newkey rsa:2048 -nodes -keyout key.pem -x509 -days 365 -out certificate.pemcopy to clipboard
```

检查已创建的证书：
```shell
openssl x509 -text -noout -in certificate.pemcopy to clipboard
```
将密钥和证书组合在 PKCS#12 (P12) 捆绑软件中：
```shell
openssl pkcs12 -inkey key.pem -in certificate.pem -export -out certificate.p12copy to clipboard
```

验证您的 P12 文件:
```shell
openssl pkcs12 -in certificate.p12 -noout -infocopy to clipboard
```



### More info:

[ernerate self signed openSSL](https://www.ibm.com/support/knowledgecenter/zh/SSWHYP_4.0.0/com.ibm.apimgmt.cmc.doc/task_apionprem_gernerate_self_signed_openSSL.html)

[Generate pkcs certificate](https://www.ibm.com/support/knowledgecenter/zh/SSWHYP_4.0.0/com.ibm.apimgmt.cmc.doc/task_apionprem_generate_pkcs_certificate.html)

