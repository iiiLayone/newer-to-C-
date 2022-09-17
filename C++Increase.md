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







































