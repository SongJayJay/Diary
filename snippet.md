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
auto handler = make_shared<Demo>(3)
```
