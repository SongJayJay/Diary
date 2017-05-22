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
