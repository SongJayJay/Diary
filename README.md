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
	3. 编译gcc/g++时内定的目录:lib:/usr/lib:/usr/local/lib
	动态运行库：
	1. gcc 参数"-Wl,-rpath"指定
	2. LD_LIBRARY_PATH, 在root根目录中的.bashrc配置环境变量。
	3. 配置文件/etc/ld.so.conf,修改完记得source
## 170330
	软中断(soft interrupt)和信号(signal handler)有什么区别？
	答：软中断是一种机器指令；信号量是一种异步或同步的编程模式
		makfile是逐语句执行的，所以当执行过程的某一条语句出错，那么就停止后续的语句执行。可以在命令行之前加"-"。如：-rm ***
		Gcov是进行代码运行的覆盖路统计工具，在进行编译链接的时候添加参数:-fprofile-arcs -ftest-coverage生成二进制文件。
		gcov主要使用.gcno和.gcda的文件，gcno是由-ftest-coverage产生的，它包含了重建基本块图和相应的块的源码的行号信息。
	*.gdca是由加了-fprofile-arcs编译参数的编译后文件所产生的，它包括了弧跳变数和其他概要信息。
	gcda文件的生成需要先执行文件才能生成。执行gcov *.cpp，屏幕上显示测试的覆盖率，并同时生成
	文件"*.cpp.gcov"。

    今天使用sudo进行切换身份执行命令时，一直提示“command not found”。经过翻阅资料发现是/etc/sudoers里边的secure_path项指定了通过sudo可以行执行的命令。所以只需要把命令的path放进去就行
## 170408
	忙着论文修改，中间耽搁了几天。今天看齐志昌的《软件工程》中的软件配置有点体会。心得如下：软件开发过程的最终结果包括以下三类信息：
	1. 计算机程序（源程序和目标程序）
	2. 描述计算机程序的文档（面向技术人员和面向用户两类）
	3. 数据结构（程序内部和外部定义两部分）
	上述所有的项目构成了一个软件配置，其中的每一项都是软件配置项（software cofiguration Item,SCI），它是配置管理的基本单位。
	软件配置管理的目的就是管理和控制SCI变化对软件质量的影响，保证软件质量。
## 170412 
	坐下来读本书，忽然看到魔数。记得上次在《深入理解JVM》碰到它。魔数是操作系统用来确认文件的类型，操作系统会在加载可执行程序之前。确认魔数是否正确，如果不正确会拒绝加载。
## 170416
	数组的初始化可以分为两种类型：静态数组初始化化和自动变量数组（局部变量数组初始化)。因为存储类型不同，所以静态数组和局部数组的初始化时间也不同。静态数组
内存地址固定，只需初始化一次。在程序执行之前链接器使用程序文件中合适的值对数组进行了初始化。如果数组未被初始化，数组元素的初始值将会自动设置为0。当程序执行时，
静态数组已经初始化完毕。
	自动变量数组存储在堆栈中，执行流每次进入它们所在的代码块时，这类变量所在
的内存位置可能并不相同，因此在程序执行之前，编译器没有办法对这些位置进行初始化。如果自动变量的声明中给出了初始值，每次当执行流进入自动变量所在作用域时，变量就被
一条隐式的赋值语句初始化。

##170503
	TDD流程：
	1. 文字描述的：测试用例列表。
		疑问：描述测试用例的格式；
		      对于涉及网络通信的模块如何描述
	2. 从测试用例列表中选取测试用例，编写测试用例代码
	3. 根据测试用例的代码，编写实现代码，使测试代码通过
	4. 2-3循环。原则：
		意图编程：假设实现代码已存在
		最小实现：实现代码刚刚好满足测试代码
		重构：发现重复代码时；硬编码；代码规范等

       用户故事：	
	1. 多个用户之间能够广播消息
	2. 架构限定：RPC、P2P
	
	结对编程：
	亚飞、剑锋：RPC
	元杰、龙谱：P2P
##170504
	TDD流程
	1. 通过用户故事描述一个功能
   	注意事项：
     	把握粒度
     	INVEST原则

	2. 将用户故事切割成一个个文字描述的测试用例
   	注意事项：
     	我们有一定的预先设计，测试用例的设计应该结合预先的设计
     	考虑测试用例的稳定性，或者说接口的稳定性，减少后期维护测试代码的代价
     	测试用例设计的方法

	3. 根据测试用例，开始编写测试代码
   	注意事项：
     	从最简单的测试用例开始，测试代码的编译和执行都是对前一阶段工作的验证 
    	 思维角度：意识编程，假设已测代码存在，先写最后的断言，再推倒出其准备动作和执行动作

	4. 编写实现代码，使测试用例通过
   	注意事项：
     	最小实现，实现代码刚刚好满足测试代码的通过
    	 注意测试用例的简单化和可读性
     	频繁提交，注意提交时机：编译通过、测试通过、重构前后；提交的log要有意义！

	5. 3-4重复
   	注意事项：
     	一旦代码有坏味道的迹象，立马重构，在重构中发现设计模式
     	重构也包括对测试代码的重构。比如：开发到一定阶段时，初始化数据的准备工作应统一完成，可将一类测试用例中的“准备”动作提取出来
     	出错则减慢速度，小步验证

