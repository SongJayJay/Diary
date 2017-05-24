```cpp
//遍历map
#include <map>
for (std::map<type1,type2>::const_iterator it = this->map.begin(); it != this->map.end(); ++it)
{...};
//C++11定义枚举拒绝了int的隐式转换
enum calss ParamType
{
	PARAMS_BY_NAME, PARAMS_BY_POSITION
}
//创建智能指针
class Demo
{
	public:
		Demo(int n){}
	...
};
auto handler = make_shared<Demo>(3)或者auto handler = shared_ptr<Demo>(new Demo(3));
//分配内存
typedef struct
{
	char * ptr;
	size_t len;
}string;

//初始化字符串s
void initString(string *s)
{
	s->len = 0;
	s->ptr = (char *)malloc(s->len+1);
	s->ptr[0] = '\0';
} 
//字符串s后,边添加字符串ptr
static size_t assignMem(void *ptr, size_t size, size_t nmemb, string *s)
{
	size_t new_len = s->len + size * nmemb;
	s->ptr = (char *) realloc(s->ptr, new_len + 1);
	memcpy(s->ptr+s->len, ptr, size * nmemb);
	s->ptr[new_len] = '\0';
	s->len = new_len;
	return size * nmemb;
}
```
### 可变参数
```cpp
typedef enum {PARAMS_BY_NAME, PARAMS_BY_POSITION} parameterDeclaration_t;
enum jsontype_t
{
	JSON_STRING = 1,
	JSON_BOOLEAN = 2,
	JSON_INTEGER = 3,
	JSON_REAL = 4,
	JSON_OBJECT = 5,
	JSON_ARRAY = 6
};
//生成处理过程
Procedure  Procedure(const string &name, parameterDeclaration_t paramType, ...)
{
	//1. 声明可变参数首地址
	va_list parameters;
	//2. 获取可变参数列表的首地址
	va_start(parameters, paramType);
	//3. 获取可变参数
	//可变参数类型为const char*，因此向后取sizeof(const char*)个字节
	const char *paramname = va_arg(parameters, const char *);
	
	jsontype_t type;
	while(paramname != NULL)
	{
		type = (jsontype_t)va_arg(parameters, int);
		this->AddParameter(paraname, type);
		paramname = va_arg(parameters, const char*);	
	}
	va_end(parameters);

	this->procedureName = name;
	this->returnType = returntype;
	this->procedureType = RPC_METHOD;
	this->parameterDeclaration paramType;
	
}
```
### 主机进程间通信-客户端
```cpp

#include <string>
#include <string.h>
#incldue <cstdlib>
#incldue <cstdio>
#incldue <unistd.h>
#include <sys/socket.h>
#include <sys/un.h>

#define BUFFER_SIZE 100;
#define DELIMITER_CHAR char(0x0A)
#define PATH_MAX 108

void UnixSocket(const std::string& message, std::string& result, std::string& path)
{
	sockaddr_un addrress;
	int socket_fd, nbytes;
	char buffer[BUFFER_SIZE];
	
	//创建套接字
	socket_fd = socket(AF_UNIX, SOCK_STREAM, 0);
	if (socket_fd < 0)
	{
		//TODO 错误处理
	}
	memset(&address, 0, sizeof(sockaddr_un));
	//设置套接字结构体
	addrress.sun_family = AF_UNIX;
	snprintf(addrress.sun_path, PATH_MAX, "%s", path.c_str());
	
	//链接服务器
	if (connect(socket_fd, (struct sockaddr *)&addrress, sizeof(sockaddr_un)) != 0)
	{
		//TODO 错误处理
	}
	//信息发送完
	bool fullyWritten = false;
	//在本地保存一份外部数据
	std::string tosend = message;
	
	do{
		//注意ssize_t和size_t的区别
		ssize_t byteWritten = write(socket_fd, toSend.c_str(), toSend.size());
		if (static_cast<size_t>(byteWritten) < toSend.size())
		{
			int len = toSend.size() - byteWritten;
			toSend = toSend.substr(byteWritten + sizeof(char), len);
		}
		else
			fullyWritten = true;
	
	   }while(!fullyWritten)
	
	do{
		nbytes = read(socket_fd, buffer, BUFFER_SIZE);
		std::string temp;
		temp.append(buffer, nbytes);
		result.append(buffer, nbytes);	
	}while(result.find(DELIMITER_CHAR) == std::string::npos)
	//关闭套接字
	close(socket_fd);
}
```
