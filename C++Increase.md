## 模板
### 概念
模板就是建立**通用的模具**，大大**提高复用性**

* 模板不可以直接使用，它只是一个框架
* 模板的通用并不是万能的

### 函数模板
* C++另一种编程思想称为 ==泛型编程== ，主要利用的技术就是模板
* C++提供两种模板机制:**函数模板**和**类模板** 

#### 函数模板语法
函数模板作用：

建立一个通用函数，其函数返回值类型和形参类型可以不具体制定，用一个**虚拟的类型**来代表。
```
template<typename T>   
函数的声明或定义
```
template  ---  声明创建模板

typename  --- 表面其后面的符号是一种数据类型，可以用class代替，无区别

T    ---   通用的数据类型，名称可以替换，通常为大写字母
```
template<typename T>
void Swap(T& a, T& b) {
	T temp = a;
	a = b;
	b = temp;
}

void test01() {
	int a = 10;
	int b = 20;
	double c = 1.2;
	double d = 2.4;
	// 1. 自动类型推导
	Swap(c, d);
	cout << "c=" << c << " d=" << d << endl;
	// 2. 显示指定类型
	Swap<int>(a, b);
	cout << "a=" << a << " b=" << b << endl;
}
```
* 函数模板利用关键字 template
* 使用函数模板有两种方式：自动类型推导、显示指定类型
* 模板的目的是为了提高复用性，将类型参数化
#### 函数模板注意事项
* 自动类型推导，必须推导出一致的数据类型T,才可以使用
* 模板必须要确定出T的数据类型，才可以使用

都用class就好
```
template<class T>
void func() {
	cout << "func的调用" << endl;
}
void test02() {
  //func();    //错误，模板不能独立使用，必须确定出T的类型
  func<int>(); //利用显示指定类型的方式，给T一个类型，才可以使用该模板
}
```
使用模板时必须确定出通用数据类型T，并且能够推导出一致的类型
#### 函数模板案例
* 利用函数模板封装一个排序的函数，可以对**不同数据类型数组**进行排序
* 排序规则从大到小，排序算法为**选择排序**
* 分别利用**char数组**和**int数组**进行测试
```
template<typename T>
void Swap(T& a, T& b) {
	T temp = a;
	a = b;
	b = temp;
}

template<class T>
void chooseSort(T tArray,int len) {
	for (int i = 0; i < len; ++i) {
		int max = i;
		for (int j = i + 1; j < len; ++j) {
			if (tArray[j] > tArray[max]) {
				max = j;
			}
		}
		if (max != i) {
			Swap(tArray[max], tArray[i]);
		}
	}
}

template<class T>
void PrintArray(T tArray, int len) {
	for (int i = 0; i < len; i++) {
		cout << tArray[i] << " ";
	}
	cout << endl;
}

void test01() {
	char charArray[] = "abcdef";
	int len = sizeof(charArray) / sizeof(char);
	chooseSort(charArray, len);
	PrintArray(charArray, len);
}


void test02() {
	int intArray[] = { 3,2,4,6,9,1 };
	int len = sizeof(intArray) / sizeof(int);
	chooseSort(intArray, len);
	PrintArray(intArray, len);
}
```
####  普通函数与函数模板的区别
* 普通函数调用时可以发生自动类型转换（隐式类型转换）
* 函数模板调用时，如果利用自动类型推导，不会发生隐式类型转换
* 如果利用显示指定类型的方式，可以发生隐式类型转换

建议使用**显示指定类型**的方式，调用函数模板，因为可以自己确定通用类型T
#### 普通函数和函数模板的调用规则
1. 如果函数模板和普通函数都可以实现，**优先调用普通函数**
2. 可以通过空模板参数列表来强制调用函数模板
3. 函数模板也可以发生重载
4. 如果函数模板可以产生更好的匹配,优先调用函数模板
```
void func(int a, int b) {
	cout << "调用普通函数" << endl;
}

template<class T>
void func(T a, T b) {
	cout << "调用模板函数" << endl;
}
template<class T>
void func(T a, T b,T c) {
	cout << "调用重载模板函数" << endl;
}

void test01() {
	int a = 2;
	int b = 5;

	func<>(a, b);  // 通过空模板参数列表来强制调用函数模板
	//int c = 2;
	//func(a, b,c);
  
  char c = '2';
  char d = '1';
  func(a, b);     // 如果函数模板可以产生更好的匹配,优先调用函数模板
}
```
既然提供了函数模板，最好就不要提供普通函数，否则容易出现二义性
#### 模板的局限性
模板的通用性并不是万能的
```
template<class T>
void f(T a,T b){
   a = b;           //传入的a和b是一个数组，就无法实现了
}                
```
```
template<class T>
void f(T a, T b){
   if(a>b){               //如果T的数据类型传入的是像Person这样的自定义数据类型，也无法正常运行
   }
}
```
因此C++为了解决这种问题，提供模板的重载，可以为这些**特定的类型**提供**具体化的模板**
```
class Person {
public:
	Person(int age, string name) :m_Age(age), m_Name(name) {}
	int m_Age;
	string m_Name;
};

template<class T>
bool com(const T &a,const T& b) {
	if (a == b) {
		return true;
	}
	else {
		return false;
	}
}

template<> bool  com(const Person& p1,const Person &p2) {
	if (p1.m_Age == p2.m_Age && p1.m_Name == p2.m_Name) {
		return true;
	}
	else {
		return false;
	}
}

void test01() {
	int a = 20;
	int b = 20;
	bool ret = com<int>(a, b);
	if (ret) {
		cout << "a == b" << endl;
	}
	else {
		cout << "a != b" << endl;
	}

	Person p1(110, "Tom");
	Person p2(10, "Tom");
	bool ret1 = com(p1, p2);
	if (ret1) {
		cout << "p1 == p2" << endl;
	}
	else {
		cout << "p1 != p2" << endl;
	}
}
```
* 利用具体化的模板，可以解决自定义类型的通用化
* 学习模板并不是为了写模板，而是在STL能够运用系统提供的模板
### 类模板
#### 类模板的语法
建立一个通用类，类中的成员 数据类型可以不具体制定，用一个**虚拟的类型**来代表。
```
template<class T>
类
```
```
template<class NameType,class AgeType>
class Person {
public:
	NameType m_Name;
	AgeType m_Age;
	Person(NameType name, AgeType age) :m_Name(name), m_Age(age) {}
};

void test01() {
	Person<string,int>p1("孙悟空", 999);
	cout << p1.m_Age << " " << p1.m_Name << endl;
}
```
#### 类模板与函数模板区别
1. 类模板没有自动类型推导的使用方式
2. 类模板在模板参数列表中可以有默认参数
```
template<class NameType,class AgeType=int>   //类模板在模板参数列表中可以有默认参数
class Person {
public:
	NameType m_Name;
	AgeType m_Age;
	Person(NameType name, AgeType age) :m_Name(name), m_Age(age) {}
};

void test01() {
	Person<string>p1("孙悟空", 999);           //类模板在模板参数列表中可以有默认参数  函数模板不可以
	cout << p1.m_Name << " " << p1.m_Age << endl;
}
```
* 类模板使用只能用显示指定类型方式
* 类模板中的模板参数列表可以有默认参数
#### 类模板中成员函数创建时机
* 普通类中的成员函数一开始就可以创建
* 类模板中的成员函数在调用时才创建
```class Person1
{
public:
	void showPerson1()
	{
		cout << "Person1 show" << endl;
	}
};

class Person2
{
public:
	void showPerson2()
	{
		cout << "Person2 show" << endl;
	}
};

template<class T>
class MyClass
{
public:
	T obj;

	//类模板中的成员函数，并不是一开始就创建的，而是在模板调用时再生成

	void fun1() { obj.showPerson1(); }
	void fun2() { obj.showPerson2(); }

};

void test01()
{
	MyClass<Person1> m;
	
	m.fun1();

	//m.fun2();//编译会出错，说明函数调用才会去创建成员函数
}
```
#### 类模板对象做函数参数
类模板实例化出的对象，向函数传参的方式

三种传入方式：

1. 指定传入的类型   --- 直接显示对象的数据类型
2. 参数模板化       --- 将对象中的参数变为模板进行传递
3. 整个类模板化     --- 将这个对象类型 模板化进行传递
```
typeid(T1).name()  // 查看T1是什么类型
```
```
template<class T1,class T2>
class Person {
public:
	T1 m_Name;
	T2 m_Age;
	Person(T1 name, T2 age) :m_Name(name), m_Age(age) {}

	void ShowPerson() {
		cout << m_Name << " " << m_Age << endl;
	}
};

//1、指定传入的类型
void PrintPerson1(Person<string, int> p) {
	p.ShowPerson();
}

//2、参数模板化
template<class T1,class T2>
void PrintPerson2(Person<T1, T2> p) {
	p.ShowPerson();
	cout << "T1的类型为：" << typeid(T1).name() << endl;
	cout << "T2的类型为：" << typeid(T2).name() << endl;
}

//3、整个类模板化
template<class T>
void PrintPerson3(T p) {
	p.ShowPerson();
	cout << "T的类型为：" << typeid(T).name() << endl;
}

void test01() {
	Person<string, int>p("孙悟空", 11);
	PrintPerson1(p);
	PrintPerson2(p);
	PrintPerson3(p);
}
```
* 通过类模板创建的对象，可以有三种方式向函数中进行传参
* 使用比较广泛是第一种：指定传入的类型

#### 类模板与继承
* 当子类继承的父类是一个类模板时，子类在声明的时候，要指定出父类中T的类型
* 如果不指定，编译器无法给子类分配内存
* 如果想灵活指定出父类中T的类型，子类也需变为类模板
```
template<class T>
class Base {
	T m;
};

//class Son :public Base<int> {   // 如果没有<int>
//                                // 父类中的T会继承到子类中，子类不是一个模板，子类占用内存空间没法算。
//};

template<class T1,class T2>
class Son :public Base<T2> {
	T1 obj;
};

void test01() {
	Son<int, char> s;
}
```
如果父类是类模板，子类需要指定出父类中T的数据类型
#### 类模板成员函数类外实现
```
template<class T1, class T2>
class Person {
public:
	Person(T1 age, T2 name);
	//{
	//	this->m_Age = age;
	//	this->m_Name = name;
	//}
	void ShowPerson();
	//{
	//	cout << m_Name << " " << m_Age;
	//}
	T1 m_Age;
	T2 m_Name;
};
template<class T1,class T2>
Person<T1, T2>::Person(T1 age, T2 name) {
	this->m_Age = age;
	this->m_Name = name;
}

template<class T1,class T2>
void Person<T1, T2>::ShowPerson() {
}
```
####  类模板分文件编写
问题：

* 类模板中成员函数创建时机是在调用阶段，导致分文件编写时链接不到

解决：

* 解决方式1：直接包含.cpp源文件
* 解决方式2：将声明和实现写到同一个文件中，并更改后缀名为.hpp，hpp是约定的名称，并不是强制

看到.hpp就知道是类模板，person.hpp中代码：
```
#pragma once
#include <iostream>
using namespace std;
#include <string>

template<class T1, class T2>
class Person {
public:
	Person(T1 name, T2 age);
	void showPerson();
public:
	T1 m_Name;
	T2 m_Age;
};

//构造函数 类外实现
template<class T1, class T2>
Person<T1, T2>::Person(T1 name, T2 age) {
	this->m_Name = name;
	this->m_Age = age;
}

//成员函数 类外实现
template<class T1, class T2>
void Person<T1, T2>::showPerson() {
	cout << "姓名: " << this->m_Name << " 年龄:" << this->m_Age << endl;
}
```
#### 类模板与友元

全局函数类内实现 - 直接在类内声明友元即可

全局函数类外实现 - 需要提前让编译器知道全局函数的存在
```
template<class T1, class T2>
class Person;

// 这里用到了person 提前要知道person的存在 person是模板 需要知道模板
template<class T1, class T2>
void ShowPerson(Person<T1, T2> p)
{
	cout << p.m_Name << " " << p.m_Age << endl;
}

template<class T1, class T2>
class Person {

	//1. 全局函数的类内实现
	//friend void ShowPerson(Person<T1,T2> p)
	//{
	//	cout << p.m_Name << " " << p.m_Age << endl;
	//}

	//2.全局函数的类外实现
	// 加模板的参数列表
	// friend void ShowPerson(Person<T1, T2> p);   如果不加，这里就是普通函数的声明，下面是函数模板的函数实现。 就报错
	friend void ShowPerson <>(Person<T1, T2> p);  
	// 如果全局函数是类外实现，需要让编译器知道他的存在
public:
	Person(T1 age, T2 name)
	{
		this->m_Age = age;
		this->m_Name = name;
	}

private:
	T1 m_Age;
	T2 m_Name;
};

void test01() {
	Person<int, string> p(18, "tom");
	ShowPerson(p);
}
```
建议全局函数做类内实现，用法简单，而且编译器可以直接识别
## STL初识
### STL的基本概念
* STL(Standard Template Library,**标准模板库**)
* STL 从广义上分为: **容器(container) 算法(algorithm) 迭代器(iterator)**
* **容器**和**算法**之间通过**迭代器**进行无缝连接。
* STL 几乎所有的代码都采用了模板类或者模板函数
### STL六大组件
STL大体分为六大组件，分别是:**容器、算法、迭代器、仿函数、适配器（配接器）、空间配置器**

1. 容器：各种数据结构，如vector、list、deque、set、map等,用来存放数据。
2. 算法：各种常用的算法，如sort、find、copy、for_each等
3. 迭代器：扮演了容器与算法之间的胶合剂。
4. 仿函数：行为类似函数，可作为算法的某种策略。
5. 适配器：一种用来修饰容器或者仿函数或迭代器接口的东西。
6. 空间配置器：负责空间的配置与管理。

### STL中容器、算法、迭代器

**容器：**置物之所也

STL**容器**就是将运用**最广泛的一些数据结构**实现出来

常用的数据结构：数组, 链表,树, 栈, 队列, 集合, 映射表 等

这些容器分为**序列式容器**和**关联式容器**两种:

​	**序列式容器**:强调值的排序，序列式容器中的每个元素均有固定的位置。
​	**关联式容器**:二叉树结构，各元素之间没有严格的物理上的顺序关系



**算法：**问题之解法也

有限的步骤，解决逻辑或数学上的问题，这一门学科我们叫做算法(Algorithms)

算法分为:**质变算法**和**非质变算法**。

质变算法：是指运算过程中会更改区间内的元素的内容。例如拷贝，替换，删除等等

非质变算法：是指运算过程中不会更改区间内的元素内容，例如查找、计数、遍历、寻找极值等等



**迭代器：**容器和算法之间粘合剂

提供一种方法，使之能够依序寻访某个容器所含的各个元素，而又无需暴露该容器的内部表示方式。

每个容器都有自己专属的迭代器

迭代器使用非常类似于指针，初学阶段我们可以先理解迭代器为指针

输入迭代器对数据的只读访问只读，支持++、==、！=

输出迭代器对数据的只写访问只写，支持++

前向迭代器读写操作，并能向前推进迭代器读写，支持++、==、！=

双向迭代器读写操作，并能向前和向后操作读写，支持++、--，

随机访问迭代器读写操作，可以以跳跃的方式访问任意数据，功能最强的迭代器读写，支持++、--、[n]、-n、<、<=、>、>=

常用的容器中迭代器种类为双向迭代器，和随机访问迭代器

### 容器算法迭代器初识
STL中最常用的容器为Vector，可以理解为数组，下面我们将学习如何向这个容器中插入数据、并遍历这个容器
```
vector                  //容器
for_each                //算法
vector<int>::iterator   //迭代器
```
#### vector存放内置数据类型
```
#include<iostream>
using namespace std;
#include<vector>
#include<algorithm>

void vPrint(int i) {
	cout << i << endl;
}

int main() {

	vector<int> v;
	v.push_back(10);
	v.push_back(20);
	v.push_back(260);
	v.push_back(10);
	v.push_back(20);
	v.push_back(260);

//每一个容器都有自己的迭代器，迭代器是用来遍历容器中的元素
//v.begin()返回迭代器，这个迭代器指向容器中第一个数据
//v.end()返回迭代器，这个迭代器指向容器元素的最后一个元素的下一个位置
//vector<int>::iterator 拿到vector<int>这种容器的迭代器类型

        //第一种遍历方式
	vector<int>::iterator pBegin = v.begin();
	vector<int>::iterator pEnd = v.end();
	while (pBegin != pEnd) {
		cout << *pBegin << " ";
		++pBegin;
	}
	cout << endl;
	//第二种遍历方式
	for (vector<int>::iterator it = v.begin(); it != v.end(); ++it) {
		cout << *it << " ";
	}
	cout << endl;
	
	//第三种使用算法，添加头文件
	for_each(v.begin(), v.end(), vPrint);

	system("pause");
	return 0;
}
```
#### Vector存放自定义数据类型
```
class Person {
public:
	Person(string name, int age) :m_Name(name), m_Age(age) {}

	string m_Name;
	int m_Age;
};

void test01() {
	vector<Person>v;
	Person p1("aaa", 10);
	Person p2("bbb", 20);
	Person p3("ccc", 30);
	Person p4("ddd", 40);
	Person p5("eee", 50);

	v.push_back(p1);
	v.push_back(p2);
	v.push_back(p3);
	v.push_back(p4);
	v.push_back(p5);

	//<>中是Person所以解引用后*it就是Person类型
	for (vector<Person>::iterator it=v.begin(); it != v.end(); ++it) {
		cout << it->m_Name << " " << it->m_Age << " " << endl;
	}
}
```
#### Vector容器嵌套容器
```
	for (vector<vector<int>>::iterator it = v.begin(); it != v.end(); ++it) {
		for (vector<int>::iterator vit = (*it).begin(); vit != (*it).end(); ++vit) {
			cout << *vit << " ";
		}
		cout << endl;
```
## STL- 常用容器
### string容器
#### string基本概念
**本质：**

* string是C++风格的字符串，而string本质上是一个类

**string和char * 区别：**

* char * 是一个指针
* string是一个类，类内部封装了char\*，管理这个字符串，是一个char*型的容器。


**特点：**

string 类内部封装了很多成员方法

例如：查找find，拷贝copy，删除delete 替换replace，插入insert

string管理char* 所分配的内存，不用担心复制越界和取值越界等，由类内部进行负责

#### string构造函数
```
string();          	       //创建一个空的字符串 例如: string str;
string(const char* s);	       //使用字符串s初始化
string(const string& str);     //使用一个string对象初始化另一个string对象
string(int n, char c);         //使用n个字符c初始化 
```
#### string赋值操作
给string字符串进行赋值
```
string& operator=(const char* s);             //char*类型字符串 赋值给当前的字符串
string& operator=(const string &s);           //把字符串s赋给当前的字符串
string& operator=(char c);                    //字符赋值给当前的字符串
string& assign(const char *s);                //把字符串s赋给当前的字符串
string& assign(const char *s, int n);         //把字符串s的前n个字符赋给当前的字符
string& assign(const string &s);              //把字符串s赋给当前字符串
string& assign(int n, char c);                //用n个字符c赋给当前字符串
```
等号赋值用的更多一些

#### string字符串拼接
实现在字符串末尾拼接字符串
```
string& operator+=(const char* str);                     //重载+=操作符
string& operator+=(const char c);                        //重载+=操作符
string& operator+=(const string& str);                   //重载+=操作符
string& append(const char *s);                           //把字符串s连接到当前字符串结尾
string& append(const char *s, int n);                    //把字符串s的前n个字符连接到当前字符串结尾
string& append(const string &s);                         //同operator+=(const string& str)
string& append(const string &s, int pos, int n);         //字符串s中从pos开始的n个字符连接到字符串结尾
//pos 从0开始的 n截取的个数
```
#### string查找和替换
* 查找：查找指定字符串是否存在
* 替换：在指定的位置替换字符串
```
int find(const string& str, int pos = 0) const;                  //查找str第一次出现位置,从pos开始查找
int find(const char* s, int pos = 0) const;                      //查找s第一次出现位置,从pos开始查找
int find(const char* s, int pos, int n) const;                   //从pos位置查找s的前n个字符第一次位置
int find(const char c, int pos = 0) const;                        //查找字符c第一次出现位置
int rfind(const string& str, int pos = npos) const;      //查找str最后一次位置,从pos开始查找
int rfind(const char* s, int pos = npos) const;              //查找s最后一次出现位置,从pos开始查找
int rfind(const char* s, int pos, int n) const;              //从pos查找s的前n个字符最后一次位置
int rfind(const char c, int pos = 0) const;                        //查找字符c最后一次出现位置
string& replace(int pos, int n, const string& str);        //替换从pos开始n个字符为字符串str
string& replace(int pos, int n,const char* s);                  //替换从pos开始的n个字符为字符串s
```
* find查找是从左往后，rfind从右往左,也是从左往右的下标
* find找到字符串后返回查找的第一个字符位置，找不到返回-1
* replace在替换时，要指定从哪个位置起，多少个字符，替换成什么样的字符串

```
	string str1 = "abcdefgde";
	str1.replace(1, 3, "1111");
	// 从1号位置起 3个字符替换为1111
```
#### string字符串比较
字符串之间的比较

**比较方式：**

* 字符串比较是按字符的ASCII码进行对比

= 返回   0

\> 返回   1 

< 返回  -1

```
int compare(const string &s) const;   //与字符串s比较
int compare(const char *s) const;      //与字符串s比较
```
```
int ret = s1.compare(s2);
```
字符串对比主要是用于比较两个字符串是否相等，判断谁大谁小的意义并不是很大
#### string字符存取
```
char& operator[](int n);      //通过[]方式取字符
char& at(int n);             //通过at方法获取字符
```
string字符串中单个字符存取有两种方式，利用 [ ] 或 at
#### string插入和删除
对string字符串进行插入和删除字符操作
**函数原型：**
```
string& insert(int pos, const char* s);                  //插入字符串
string& insert(int pos, const string& str);              //插入字符串
string& insert(int pos, int n, char c);                  //在指定位置插入n个字符c
string& erase(int pos, int n = npos);                    //删除从Pos开始的n个字符 * `string& 
```
```
void test01()
{
	string str = "hello";
	str.insert(1, "111");
	cout << str << endl;   //输出 h111ello

	str.erase(1, 3);  //从1号位置开始3个字符
	cout << str << endl;     /// hello
}
```
插入和删除的起始下标都是从0开始
#### string字串
从字符串中获取想要的子串
```
string substr(int pos = 0, int n = npos) const;   //返回由pos开始的n个字符组成的字符串
```
```
void test01()
{

	string str = "abcdefg";
	string subStr = str.substr(1, 3);
	cout << "subStr = " << subStr << endl;  // bcd

	string email = "hello@sina.com";
	int pos = email.find("@");
	string username = email.substr(0, pos);
	cout << "username: " << username << endl; //hello

}
```
### vector容器
#### vector基本概念
**功能：**

* vector数据结构和**数组非常相似**，也称为**单端数组**

**vector与普通数组区别：**

* 不同之处在于数组是静态空间，而vector可以**动态扩展**

**动态扩展：**

* 并不是在原空间之后续接新空间，而是找更大的内存空间，然后将原数据拷贝新空间，释放原空间

vector容器的迭代器是支持随机访问的迭代器
#### vector构造函数
````
vector<T> v;                         //采用模板实现类实现，默认构造函数
vector(v.begin(), v.end());          //将v[begin(), end())区间中的元素拷贝给本身。
vector(n, elem);                     //构造函数将n个elem拷贝给本身。
vector(const vector &vec);           //拷贝构造函数。
````
```
void printVector(vector<int>& v) {

	for (vector<int>::iterator it = v.begin(); it != v.end(); it++) {
		cout << *it << " ";
	}
	cout << endl;
}

void test01()
{
	vector<int> v1; //无参构造
	for (int i = 0; i < 10; i++)
	{
		v1.push_back(i);
	}
	printVector(v1);

	vector<int> v2(v1.begin(), v1.end());
	printVector(v2);

	vector<int> v3(10, 100);
	printVector(v3);
	
	vector<int> v4(v3);
	printVector(v4);
}
```
#### vector赋值操作
```
vector& operator=(const vector &vec); //重载等号操作符
assign(beg, end);                     //将[beg, end)区间中的数据拷贝赋值给本身。
assign(n, elem);                      //将n个elem拷贝赋值给本身。
``` 
```
void test01()
{
	vector<int> v1; //无参构造
	for (int i = 0; i < 10; i++)
	{
		v1.push_back(i);
	}
	printVector(v1);

	vector<int>v2;
	v2 = v1;
	printVector(v2);

	vector<int>v3;
	v3.assign(v1.begin(), v1.end());
	printVector(v3);

	vector<int>v4;
	v4.assign(10, 100);
	printVector(v4);
	
	//0 1 2 3 4 5 6 7 8 9
	//0 1 2 3 4 5 6 7 8 9
	//0 1 2 3 4 5 6 7 8 9
	//100 100 100 100 100 100 100 100 100 100
}
```
vector赋值方式比较简单，使用operator=，或者assign都可以

#### vector容量和大小
对vector容器的容量和大小操作
```
empty();                             //判断容器是否为空
capacity();                          //容器的容量 capacity>=size
size();                              //返回容器中元素的个数
resize(int num);                     //重新指定容器的长度为num，若容器变长，则以默认值填充新位置。
				     //如果容器变短，则末尾超出容器长度的元素被删除。
resize(int num, elem);               //重新指定容器的长度为num，若容器变长，则以elem值填充新位置				                     //如果容器变短，则末尾超出容器长度的元素被删除
```
```
void test01()
{
	vector<int> v1;
	for (int i = 0; i < 10; i++)
	{
		v1.push_back(i);
	}
	printVector(v1);
	if (v1.empty())
	{
		cout << "v1为空" << endl;
	}
	else
	{
		cout << "v1不为空" << endl;
		cout << "v1的容量 = " << v1.capacity() << endl;
		cout << "v1的大小 = " << v1.size() << endl;
	}

	//resize 重新指定大小 ，若指定的更大，默认用0填充新位置，可以利用重载版本替换默认填充
	v1.resize(15, 10);
	printVector(v1);

	//resize 重新指定大小 ，若指定的更小，超出部分元素被删除
	v1.resize(5);
	printVector(v1);
}
```
* 判断是否为空  --- empty
* 返回元素个数  --- size
* 返回容器容量  --- capacity
* 重新指定大小  ---  resize

#### vector插入和删除
对vector容器进行插入、删除操作
```
push_back(ele);                                         //尾部插入元素ele
pop_back();                                             //删除最后一个元素
insert(const_iterator pos, ele);                        //迭代器指向位置pos插入元素ele
insert(const_iterator pos, int count,ele);              //迭代器指向位置pos插入count个元素ele
erase(const_iterator pos);`                             //删除迭代器指向的元素
erase(const_iterator start, const_iterator end);        //删除迭代器从start到end之间的元素
clear();                                                //删除容器中所有元素
```
```
void test01()
{
	vector<int> v1;
	//尾插
	v1.push_back(10);
	v1.push_back(20);
	v1.push_back(30);
	v1.push_back(40);
	v1.push_back(50);
	printVector(v1);
	//尾删
	v1.pop_back();
	printVector(v1);
	//插入
	v1.insert(v1.begin(), 100);        //迭代器
	printVector(v1);

	v1.insert(v1.begin(), 2, 1000);
	printVector(v1);

	//删除
	v1.erase(v1.begin());
	printVector(v1);

	//清空
	v1.erase(v1.begin(), v1.end());  // 类似于清空clear 只打出一个换行
	v1.clear();                      // 
	printVector(v1);
	
	//10 20 30 40 50
	//10 20 30 40
	//100 10 20 30 40
	//1000 1000 100 10 20 30 40
	//1000 100 10 20 30 40
}
```
#### vector数据存取
对vector中的数据的存取操作
```
at(int idx);      //返回索引idx所指的数据
operator[];       //返回索引idx所指的数据
front();          //返回容器中第一个数据元素
back();           //返回容器中最后一个数据元素
```
* 除了用迭代器获取vector容器中元素，[ ]和at也可以
* front返回容器第一个元素
* back返回容器最后一个元素
```
void test01()
{
	vector<int>v1;
	for (int i = 0; i < 10; i++)
	{
		v1.push_back(i);
	}

	for (int i = 0; i < v1.size(); i++)
	{
		cout << v1[i] << " ";
	}
	cout << endl;

	for (int i = 0; i < v1.size(); i++)
	{
		cout << v1.at(i) << " ";
	}
	cout << endl;

	cout << "v1的第一个元素为： " << v1.front() << endl;
	cout << "v1的最后一个元素为： " << v1.back() << endl;
}

//0 1 2 3 4 5 6 7 8 9
//0 1 2 3 4 5 6 7 8 9
//v1的第一个元素为： 0
//v1的最后一个元素为： 9
```
#### vector互换容器
实现两个容器内元素进行互换
```
swap(vec);      // 将vec与本身的元素互换
```
```
void test01()
{
	vector<int>v1;
	for (int i = 0; i < 10; i++)
	{
		v1.push_back(i);
	}
	printVector(v1);

	vector<int>v2;
	for (int i = 10; i > 0; i--)
	{
		v2.push_back(i);
	}
	printVector(v2);

	//互换容器
	cout << "互换后" << endl;
	v1.swap(v2);
	printVector(v1);
	printVector(v2);
}


void test02()
{
	vector<int> v;
	for (int i = 0; i < 100000; i++) {
		v.push_back(i);
	}

	cout << "v的容量为：" << v.capacity() << endl;
	cout << "v的大小为：" << v.size() << endl;

	v.resize(3);

	cout << "v的容量为：" << v.capacity() << endl;
	cout << "v的大小为：" << v.size() << endl;

	//收缩内存
	vector<int>(v).swap(v); //vector<int>(v)匿名对象
	//用v目前所占的个数来初始化匿名对象
	// swap容器的交换
	//当前行执行完 系统会把内存回收

	cout << "v的容量为：" << v.capacity() << endl;
	cout << "v的大小为：" << v.size() << endl;
}
//0 1 2 3 4 5 6 7 8 9
//10 9 8 7 6 5 4 3 2 1
//互换后
//10 9 8 7 6 5 4 3 2 1
//0 1 2 3 4 5 6 7 8 9
//v的容量为：138255
//v的大小为：100000
//v的容量为：138255
//v的大小为：3
//v的容量为：3
//v的大小为：3
```
swap可以使两个容器互换，可以达到实用的收缩内存效果
#### vector预留空间
减少vector在动态扩展容量时的扩展次数

```
reserve(int len);  //容器预留len个元素长度，预留位置不初始化，元素不可访问。一访问就报错
```
```
void test01()
{
	vector<int> v;

	//预留空间
	v.reserve(100000);

	int num = 0;
	int* p = NULL;
	for (int i = 0; i < 100000; i++) {
		v.push_back(i);
		if (p != &v[0]) {
			p = &v[0];
			num++;
		}
	}

	cout << "num:" << num << endl;
}
```

如果数据量较大，可以一开始利用reserve预留空间
### deque容器
双端数组，可以对头端进行插入删除操作
**deque与vector区别：**

* vector对于头部的插入删除效率低，数据量越大，效率越低
* deque相对而言，对头部的插入删除速度会比vector快
* vector访问元素时的速度会比deque快,这和两者内部实现有关

deque内部工作原理:

deque内部有个**中控器**，维护每段缓冲区中的内容，缓冲区中存放真实数据

中控器维护的是每个缓冲区的地址，使得使用deque时像一片连续的内存空间

deque容器的迭代器也是支持随机访问的
#### deque构造函数
```
deque<T> deqT;                     //默认构造形式
deque(beg, end);                   //构造函数将[beg, end)区间中的元素拷贝给本身。
deque(n, elem);                    //构造函数将n个elem拷贝给本身。
deque(const deque &deq);           //拷贝构造函数
```

```
void printDeque(const deque<int>& d)  //加了const成了只读 所以要用只读的迭代器const_iterator 
{
                                      //也可以把两个const都去掉
	for (deque<int>::const_iterator it = d.begin(); it != d.end(); it++) {
		cout << *it << " ";
	}
	cout << endl;
}
//deque构造
void test01() {
	deque<int> d1; //无参构造函数
	for (int i = 0; i < 10; i++)
	{
		d1.push_back(i);
	}
	printDeque(d1);
	deque<int> d2(d1.begin(),d1.end());
	printDeque(d2);

	deque<int>d3(10,100);
	printDeque(d3);

	deque<int>d4 = d3;
	printDeque(d4);
}
//0 1 2 3 4 5 6 7 8 9
//0 1 2 3 4 5 6 7 8 9
//0 1 2 3 4 5 6 7 8 9
//100 100 100 100 100 100 100 100 100 100
```
#### deque 赋值操作
```
deque& operator=(const deque &deq);    //重载等号操作符
assign(beg, end);                      //将[beg, end)区间中的数据拷贝赋值给本身。
assign(n, elem);                       //将n个elem拷贝赋值给本身。
```
```
void test01()
{
	deque<int> d1;
	for (int i = 0; i < 10; i++)
	{
		d1.push_back(i);
	}
	printDeque(d1);

	deque<int>d2;
	d2 = d1;
	printDeque(d2);

	deque<int>d3;
	d3.assign(d1.begin(), d1.end());
	printDeque(d3);

	deque<int>d4;
	d4.assign(10, 100);
	printDeque(d4);

}
//0 1 2 3 4 5 6 7 8 9
//0 1 2 3 4 5 6 7 8 9
//0 1 2 3 4 5 6 7 8 9
//100 100 100 100 100 100 100 100 100 100
```
#### deque大小操作
```
deque.empty();                        //判断容器是否为空
deque.size();                         //返回容器中元素的个数
deque.resize(num);                    //重新指定容器的长度为num,若容器变长，则以默认值填充新位置。
		                      //如果容器变短，则末尾超出容器长度的元素被删除。
deque.resize(num, elem);              //重新指定容器的长度为num,若容器变长，则以elem值填充新位置。
                                      //如果容器变短，则末尾超出容器长度的元素被删除。
```
```
void test01()
{
	deque<int> d1;
	for (int i = 0; i < 10; i++)
	{
		d1.push_back(i);
	}
	printDeque(d1);

	//判断容器是否为空
	if (d1.empty()) {
		cout << "d1为空!" << endl;
	}
	else {
		cout << "d1不为空!" << endl;
		//统计大小
		cout << "d1的大小为：" << d1.size() << endl;
	}

	//重新指定大小
	d1.resize(15, 1);
	printDeque(d1);

	d1.resize(5);
	printDeque(d1);
}

//0 1 2 3 4 5 6 7 8 9
//d1不为空!
//d1的大小为：10
//0 1 2 3 4 5 6 7 8 9 1 1 1 1 1
//0 1 2 3 4
```
* deque没有容量的概念
* 判断是否为空   --- empty
* 返回元素个数   --- size
* 重新指定个数   --- resize
#### deque 插入和删除
```
两端插入操作：
push_back(elem);         //在容器尾部添加一个数据
push_front(elem);        //在容器头部插入一个数据
pop_back();              //删除容器最后一个数据
pop_front();             //删除容器第一个数据

指定位置操作：
insert(pos,elem);         //在pos位置插入一个elem元素的拷贝，返回新数据的位置。
insert(pos,n,elem);       //在pos位置插入n个elem数据，无返回值。
insert(pos,beg,end);      //在pos位置插入[beg,end)区间的数据，无返回值。
clear();                  //清空容器的所有数据
erase(beg,end);           //删除[beg,end)区间的数据，返回下一个数据的位置。
erase(pos);               //删除pos位置的数据，返回下一个数据的位置。
```
```
//两端操作
void test01()
{
	deque<int> d;
	//尾插
	d.push_back(10);
	d.push_back(20);
	//头插
	d.push_front(100);
	d.push_front(200);

	printDeque(d);

	//尾删
	d.pop_back();
	//头删
	d.pop_front();
	printDeque(d);
}

//插入
void test02()
{
	deque<int> d;
	d.push_back(10);
	d.push_back(20);
	d.push_front(100);
	d.push_front(200);
	printDeque(d);

	d.insert(d.begin(), 1000);
	printDeque(d);

	d.insert(d.begin(), 2,10000);
	printDeque(d);

	deque<int>d2;
	d2.push_back(1);
	d2.push_back(2);
	d2.push_back(3);

	d.insert(d.begin(), d2.begin(), d2.end());
	printDeque(d);

}

//删除
void test03()
{
	deque<int> d;
	d.push_back(10);
	d.push_back(20);
	d.push_front(100);
	d.push_front(200);
	printDeque(d);

	d.erase(d.begin());
	printDeque(d);

	d.erase(d.begin(), d.end());
	d.clear();
	printDeque(d);
}
//200 100 10 20
//100 10
//200 100 10 20
//1000 200 100 10 20
//10000 10000 1000 200 100 10 20
//1 2 3 10000 10000 1000 200 100 10 20
//200 100 10 20
//100 10 20
//
```
#### deque 数据存取
```
at(int idx);      //返回索引idx所指的数据
operator[];       //返回索引idx所指的数据
front();          //返回容器中第一个数据元素
back();           //返回容器中最后一个数据元素
```
```
void test01()
{
	deque<int> d;
	d.push_back(10);
	d.push_back(20);
	d.push_front(100);
	d.push_front(200);

	for (int i = 0; i < d.size(); i++) {
		cout << d[i] << " ";
	}
	cout << endl;
	for (int i = 0; i < d.size(); i++) {
		cout << d.at(i) << " ";
	}
	cout << endl;
	cout << "front:" << d.front() << endl;
	cout << "back:" << d.back() << endl;
//200 100 10 20
//200 100 10 20
//front:200
//back:20
}
```
- 除了用迭代器获取deque容器中元素，[ ]和at也可以
- front返回容器第一个元素
- back返回容器最后一个元素

#### deque 排序

```
sort(iterator beg, iterator end)  //对beg和end区间内元素进行排序
```
```
void test01()
{

	deque<int> d;
	d.push_back(10);
	d.push_back(20);
	d.push_front(100);
	d.push_front(200);

	printDeque(d);
	sort(d.begin(), d.end());
	printDeque(d);
//200 100 10 20
//10 20 100 200
}
```
sort算法非常实用，使用时包含头文件 algorithm即可





































