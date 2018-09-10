# Morden cpp tutorial

## 第2章 语言可用性的强化

1. 常量
	1. `nullptr`替代`NULL`
	2. `constexpr`声明常量表达式，`constexpr`的函数可以使用递归。从C++14开始，`constexpr`函数可以在内部使用局部变量、循环和分支等简单语句。
2. 变量及其初始化
	1. C++17开始，可以在 if/switch 的条件语句中声明变量
	2. 初始化列表
		* 传统C++：
		  	1. 数组、POD（Plain Old Data）可以使用`{}`进行初始化
		  	2. 类对象：拷贝构造函数 或 `()`进行初始化
		* C++ 11：
		  	1. 统一语法初始化`{}`

		  		`Foo foo{3, 4};`
		  	2. 初始化列表构造函数.

		  		```
		  		MagicFoo(std::initializer_list<int> list) {
        			for (std::initializer_list<int>::iterator it = list.begin();
             			it != list.end(); ++it)
            			vec.push_back(*it);
    			}
    			MagicFoo magicFoo = {1, 2, 3, 4, 5};
		  		```
		  	3. 普通函数参数

			  	```
				void foo(std::initializer_list<int> list) {
	     			for (std::initializer_list<int>::iterator it = list.begin();
	     			it != list.end(); ++it) {
	     				vec.push_back(*it);
	     			}
			   }

				magicFoo.foo({6,7,8,9});
				```
	3. 结构化绑定

		```
		std::tuple<int, double, std::string> f() {
		    return std::make_tuple(1, 2.3, "456");
		}

		int main() {
		    auto [x, y, z] = f();
		    std::cout << x << ", " << y << ", " << z << std::endl;
		    return 0;
		}
		```
3. 类型推导
	1. auto: 自动推导变量类型
		1. 不能用于函数形参
		2. 不能用于推导数组类型
	2. decltype: 自动推导表达式类型

		`decltype(表达式)`
	3. 尾返回类型推导.

	   C++11引入尾返回类型
	   C++14可以直接让普通函数具备返回值推导

		```
		// C++11
		template<typename T, typename U>
			auto add2(T x, U y) -> decltype(x+y){
    		return x + y;
		}

		// C++14
		template<typename T, typename U>
		auto add3(T x, U y){
    		return x + y;
		}
		```
	4. decltype(auto)
4. 控制流
	1. if constexpr: 代码在编译时判断是否是常量表达式
	2. range for

		```
		for (auto element : vec)
	        std::cout << element << std::endl; // read only
	    for (auto &element : vec) {
	        element += 1;                      // writeable
	    }
		```
5. 模板
	1. 模板显式实例化

		```
		template class std::vector<bool>;          // 强行实例化
		extern template class std::vector<double>; // 不在该当前编译文件中实例化模板
		```
	2. 尖括号“>”: C++11开始，可以连续右尖括号
	3. using： typedef无法为模板定义别名，因为“模板是用来产生类型的，本身并不是一种类型”
	4. 默认模板参数： C++ 11可以指定模板的默认参数

		```
		template<typename T = int, typename U = int>
		auto add(T x, U y) -> decltype(x+y) {
		    return x+y;
		}
		```
	5. 变长参数模板
		1. 定义

			`template<typename... Ts> class Magic;`
		2. 获取参数：
			1. 使用sizeof...获取长度
			2. 对参数解包
				1. 递归

					```
					// C++ 17
					template<typename T0, typename... T>
					void printf(T0 t0, T... t) {
					    std::cout << t0 << std::endl;
					    if constexpr (sizeof...(t) > 0) printf(t...);
					}
					```
				2. 初始化列表展开
		3. 折叠表达式（C++17）

			```
			#include <iostream>
			template<typename ... T>
			auto sum(T ... t) {
			    return (t + ...);
			}
			int main() {
			    std::cout << sum(1, 2, 3, 4, 5, 6, 7, 8, 9, 10) << std::endl;
			}
			```
6. 面向对象
	1. 委托构造： 构造函数可以调用同一个类中的另一个构造函数
	2. 继承构造：C++ 11利用关键字using引入继承构造函数
	3. 显式虚函数重载
		1. override: 重载虚函数
		2. final
			* 防止类被继续继承
			* 防止虚函数继续重载
	4. 显式禁用默认函数
		1. default： 显式声明使用编译器生成的函数
		2. delete： 显式声明拒绝编译器生成的函数
	5. 强类型枚举

		```
		enum class new_enum : unsigned int {
		    value1,
		    value2,
		    value3 = 100,
		    value4 = 100
		};
		```

## 第3章 语言运行期的强化

1. Lambda表达式
	1. 值捕获
		1. 变量要可拷贝
		2. 被捕获的变量在lambda表达式被创建时拷贝，而非调用时才拷贝
	2. 引用捕获：引用捕获保存的是引用，值会发生变化
	3. 隐式捕获：可在捕获列表写一个`&`或`=`向编译器声明采用`引用捕获`或`值捕获`
	4. 表达式捕获
	5. 泛型Lambda：从C++14开始，Lambda函数的形式参数可以使用`auto`关键字来产生意义上的泛型。

		```
		auto add = [](auto x, auto y) {
			return x + y;
		};
		add(1, 2);
		add(1.1, 2.2);
		```
2. 函数对象包装器
	1. std::function: 将能够被调用的对象的类型，统一称为可调用类型。

		```
		int foo(int para) {
			return para;
		}

		int main() {
			// 处理函数
			std::function<int(int)> func = foo;

			// 处理lamda函数
			int important = 10;
			std::function<int(int)> func2 = [&](int value) -> int {
				return 1 + value + important;
			}
		}
		```
	2. std::bind/std::placeholder: 将部分调用参数提前绑定到函数上，形成一个新的兑现。

		```
		int foo(int a, int b, int c) {
			return a + b + c;
		}

		int main() {
			// 将参数1，2绑定到foo上，使用placeholder对第一个参数进行占位
			auto bindFoo = std::bind(foo, std::placeholder::_1, 1, 2);
			// 调用bindFoo，只需提供第一个参数
			bindFoo(1);
		}
		```
	3. 右值引用
		1. 为了支持移动操作，引入右值引用。
		2. 所谓右值引用，就是必须绑定到右值的引用。使用`&&`而不是`&`来获得右值引用。
		3. 右值引用只能绑定到一个将要销毁的对象，因此可以自由地将右值引用的资源“移动”到另一个对象中。
		4. 左值引用不能绑定到要求转换的表达式、字面常量或返回右值的表达式。右值引用可以绑定到这类表达式上，但是不能将右值引用直接绑定到一个左值上。
		5. 左值具有持久的状态，右值要么是字面常量，要么是表达式求值过程中创建的临时对象。
		6. std::move告诉编译器：我们有一个左值，但我们希望像一个右值一样处理它。
			`int &&rr3 = std::move(rr1);`
		7. 可以销毁一个移后源对象，也可以赋予新值，但不能读取它的值。
