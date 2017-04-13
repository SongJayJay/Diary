## Diary
	在这里记录每天的编程笔记和日志记录
	增加nmon
## 170321
	   今天要去做验签，传给上海CA的证书格式得是个证书链而且还是个p7b格式的。赶紧去查查怎么生成证书链吧，还是p7b格式的
	p7b证书链的生成太纠结了，一天也没有搞定
	   昨天在编译代码的时候发现引用动态库的时候出现错误，指定动态库运行路径的时候参数使用错误，"-Wl,-rpath"写成 "-Wl,rpath"半天才发现这个问题
## 170323
	  自己使用CA服务器进行签发证书，openssl的默认配置文件是：/etc/pki/tls/openssl.cnf。
	  可以设置证书的默认请求信息。/etc/pki/CA/index.txt为证书的数据库，/etc/pki/CA/serial为证书的序列号。
	/etc/pki/CA/cls为吊销证书目录, 这些可以在openssl.cnf中配置。
	首先,CA生成一个自签证书，需要先拿到私钥；
		openssl genrsa -out ca.key 2048
	第二步：使用私钥生成自签证书。
		openssl req -new -x509 -key private/cakey.pem -out cacert.pem -days 3650
	证书的申请和签发：
	第一步：构建私钥
		openssl genrsa 1024 > httpd.key
	第二步：生成证书签名请求
		 openssl req -new -key httpd.key -out httpd.csr
	第三步：将请求传递给CA，CA签名
		 openssl ca -in /tmp/httpd.csr -out /tmp/httpd.crt -days 3650
	第四步：查看证书
		 openssl x509 -in httpd.crt -noout -text
	查看证书
		证书和CSR文件以及PEM格式的编码,是不易阅读的，
		查看CSR条目：
		openssl req -text -noout -verify -in domin.csr
		查看证书条目
		openssl x509 -text -noout -in domin.crt
	证书验签，使用此命令可以验证证书（domin.crt）是由一个特定的CA证书（ca.crt）签发
		openssl verify -verbose -CAFile ca.crt domin.crt
	私钥加/解密
		openssl rsa -des3 -in unencrypted.key -out encrypted.key
		openssl rsa -in encrypted.key -out decrypted.key
	转换证书格式
		DER->PEM
		openssl x509 \
		       -inform der -in domain.der \
		              -out domain.crt
		PEM->PKCS7
		openssl crl2pkcs7 -nocrl \
		       -certfile domain.crt \
		              -certfile ca-chain.crt \
			             -out domain.p7b
		PKCS7->PEM
		openssl pkcs7 \
		       -in domain.p7b \
		              -print_certs -out domain.crt
		请注意，如果您的PKCS7文件中有多个项目（例如证书和CA中间证书），则创建的PEM文件将包含其中的所有项目。
		PEM->PKCS12
		私钥（使用此命令domain.key ）和证书（ domain.crt ），并结合成一个PKCS12文件(domain.pfx)
		openssl pkcs12 \
		       -inkey domain.key \
		              -in domain.crt \
			             -export -out domain.pfx
		系统将提示您输出导出密码，您可以留空。 请注意，您可以通过一个单一的PEM文件（串联证书加在一起的证书链到PKCS12文件domain.crt在这种情况下）。
		PKCS12文件（也称为PFX文件）通常用于在Micrsoft IIS（Windows）中导入和导出证书链。
		PKCS12->PEM
		openssl pkcs12 \
		       -in domain.pfx \
		              -nodes -out domain.combined.crt
		请注意，如果您的PKCS12文件中有多个项目（例如证书和私钥），则创建的PEM文件将包含其中的所有项目。
	FAQ：
	1. I am unable to access the /etc/pki/CA/newcerts directory
	   mkdir  /etc/pki/CA/newcerts
	2. unable to load number from /etc/pki/CA/serial
	   echo "00">/etc/pki/CA/serial

	

	
	 
## 170329
	配置71和72相同的环境。头文件默认存放目录为:/usr/local/include:/usr/include。这是有配置文件specs文件指定的，一般通过gcc -v或者 info gcc显示specs文件的位置。
	头文件：
	1. -I
	2. C_INCLUDE_PATH, CPLUS_INCLUDE, OBJC_INCLUDE_PATH
	库文件：
	1. -L
	2. LIBRARY_PATH
	动态运行库：
	1. gcc 参数"-Wl,-rpath"指定
	2. LD_LIBRARY_PATH, 在root根目录中的.bashrc配置环境变量。
	3. 配置文件/etc/ld.so.conf,修改完记得source
	
