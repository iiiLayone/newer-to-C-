### map/ multimap容器

* map中所有元素都是pair
* pair中第一个元素为key（键值），起到索引作用，第二个元素为value（实值）
* 所有元素都会根据元素的键值自动排序

**本质：**

* map/multimap属于**关联式容器**，底层结构是用二叉树实现。

**优点：**

* 可以根据key值快速找到value值

map和multimap**区别**：

- map不允许容器中有重复key值元素
- multimap允许容器中有重复key值元素
- 只区分key值不区分value值
####  map构造和赋值
```
map<T1, T2> mp;                  //map默认构造函数: 
map(const map &mp);              //拷贝构造函数
map& operator=(const map &mp);   //重载等号操作符
```
```
void printMap(map<int,int>&m)
{
	for (map<int, int>::iterator it = m.begin(); it != m.end(); it++)
	{
		cout << "key = " << it->first << " value = " << it->second << endl;
	}
	cout << endl;
}

void test01()
{
	map<int,int>m; //默认构造
	m.insert(pair<int, int>(1, 10));
	m.insert(pair<int, int>(2, 20));
	m.insert(pair<int, int>(3, 30));
	printMap(m);

	map<int, int>m2(m); //拷贝构造
	printMap(m2);

	map<int, int>m3;
	m3 = m2; //赋值
	printMap(m3);
}
```
map中所有元素都是成对出现，插入数据时候要使用**对组**
#### map大小和交换
```
size();          //返回容器中元素的数目
empty();        //判断容器是否为空
swap(st);      //交换两个集合容器
```
```
void test01()
{
	map<int, int>m;
	m.insert(pair<int, int>(1, 10));
	m.insert(pair<int, int>(2, 20));
	m.insert(pair<int, int>(3, 30));

	if (m.empty())
	{
		cout << "m为空" << endl;
	}
	else
	{
		cout << "m不为空" << endl;
		cout << "m的大小为： " << m.size() << endl;
	}
}
//交换
void test02()
{
	map<int, int>m;
	m.insert(pair<int, int>(1, 10));
	m.insert(pair<int, int>(2, 20));
	m.insert(pair<int, int>(3, 30));

	map<int, int>m2;
	m2.insert(pair<int, int>(4, 100));
	m2.insert(pair<int, int>(5, 200));
	m2.insert(pair<int, int>(6, 300));

	cout << "交换前" << endl;
	printMap(m);
	printMap(m2);

	cout << "交换后" << endl;
	m.swap(m2);
	printMap(m);
	printMap(m2);
}
```
####  map插入和删除
```
insert(elem);           //在容器中插入元素。
clear();                //清除所有元素
erase(pos);             //删除pos迭代器所指的元素，返回下一个元素的迭代器。
erase(beg, end);        //删除区间[beg,end)的所有元素 ，返回下一个元素的迭代器。
erase(key);             //删除容器中值为key的元素。没有就不删了
```
```
void test01()
{
	//插入
	map<int, int> m;
	//第一种插入方式
	m.insert(pair<int, int>(1, 10));
	//第二种插入方式
	m.insert(make_pair(2, 20));
	//第三种插入方式
	m.insert(map<int, int>::value_type(3, 30));
	//第四种插入方式
	m[4] = 40;
	printMap(m);

	//删除
	m.erase(m.begin());
	printMap(m);

	m.erase(3);
	printMap(m);

	//清空
	m.erase(m.begin(), m.end());
	m.clear();
	printMap(m);
}

//key = 1 value = 10
//key = 2 value = 20
//key = 3 value = 30
//key = 4 value = 40

//key = 2 value = 20
//key = 3 value = 30
//key = 4 value = 40

//key = 2 value = 20
//key = 4 value = 40
```
[]不建议插入，用途是通过key访问value
#### map查找和统计
```
find(key);     //查找key是否存在,若存在，返回该键的元素的迭代器；若不存在，返回set.end();
count(key);    //统计key的元素个数
```
```
void test01()
{
	map<int, int>m; 
	m.insert(pair<int, int>(1, 10));
	m.insert(pair<int, int>(2, 20));
	m.insert(pair<int, int>(3, 30));

	//查找
	map<int, int>::iterator pos = m.find(3);

	if (pos != m.end())
	{
		cout << "找到了元素 key = " << (*pos).first << " value = " << (*pos).second << endl;
	}
	else
	{
		cout << "未找到元素" << endl;
	}

	//统计
	int num = m.count(3);
	cout << "num = " << num << endl;
}
找到了元素 key = 3 value = 30
num = 1
```
#### map容器排序
map容器默认排序规则为 按照**key**值进行 **从小到大**排序，掌握如何改变排序规则

主要技术点:**

- 利用仿函数，可以改变排序规则


```
class myCompare {
public:
	bool operator()(int v1,int v2)const {
		return v1 > v2;
	}

};

void test01()
{
	map<int, int,myCompare>m;
	m.insert(pair<int, int>(2, 3));
	m.insert(pair<int, int>(1, 10));
	
	m.insert(pair<int, int>(3, 7));
	m.insert(pair<int, int>(4, 56));
	for (map<int, int>::iterator it = m.begin(); it != m.end(); ++it) {
		cout << it->first << " " << it->second << endl;
	}
}
```
* 利用仿函数可以指定map容器的排序规则
* 对于自定义数据类型，map必须要指定排序规则,同set容器
#### 案例 员工分组
1. 创建10名员工，放到vector中
2. 遍历vector容器，取出每个员工，进行随机分组
3. 分组后，将员工部门编号作为key，具体员工作为value，放入到multimap容器中
4. 分部门显示员工信息
```
class Worker {
public:
	string m_Name;
	int m_Salary;
};

void CreatWorker(vector<Worker>& v) {
	string nameSeed = "ABCDEFGHIJ";
	for (int i = 0; i < 10; ++i) {
		Worker w;
		w.m_Name = "员工";
		w.m_Name += nameSeed[i];
		w.m_Salary = rand() % 10000 + 10000;
		v.push_back(w);
	}
}

void SetGroup(vector<Worker>& v, multimap<int, Worker>& m) {
	for (vector<Worker>::iterator it = v.begin(); it != v.end(); ++it) {
		int num = rand() % 3;
		m.insert(pair<int, Worker>(num, *it));
	}
}

void showWorkerByGroup(multimap<int, Worker>& m) {
	cout << "部门0：" << endl;
	multimap<int, Worker>::iterator it0 = m.find(0);   //find 的使用
	int num0 = m.count(0);                             //count的使用
	int index = 0;
	for (; it0 != m.end() && index < num0; ++it0, ++index) {
		cout << it0->second.m_Name << " : " << it0->second.m_Salary << endl;
	}

	cout << "部门1：" << endl;
	multimap<int, Worker>::iterator it1 = m.find(1);
	int num1 = m.count(1);
	int index1 = 0;
	for (; it1 != m.end() && index1 < num1; ++it1, ++index1) {
		cout << it1->second.m_Name << " : " << it1->second.m_Salary << endl;
	}

	cout << "部门2：" << endl;
	multimap<int, Worker>::iterator it2 = m.find(2);
	int num2 = m.count(2);
	int index2 = 0;
	for (; it2 != m.end() && index2 < num2; ++it2, ++index2) {
		cout << it2->second.m_Name << " : " << it2->second.m_Salary << endl;
	}
}

void test01() {
	vector<Worker>v;
	CreatWorker(v);
	//for (vector<Worker>::iterator it = v.begin(); it != v.end(); ++it) {
	//	cout << "姓名：" << it->m_Name << "  " << "工资：" << it->m_Salary << endl;
	//}
	multimap<int, Worker>m;
	SetGroup(v, m);
	showWorkerByGroup(m);
}
```
find和count的使用很巧妙
## STL 函数对象

**概念：**

* 重载**函数调用操作符**的类，其对象常称为**函数对象**
* **函数对象**使用重载的()时，行为类似函数调用，也叫**仿函数**

**本质：**

函数对象(仿函数)是一个**类**，不是一个函数

#### 函数对象的使用

* 函数对象在使用时，可以像普通函数那样调用, 可以有参数，可以有返回值
* 函数对象超出普通函数的概念，函数对象可以有自己的状态
* 函数对象可以作为参数传递
```
class myAdd {
public:
	int operator()(int v1,int v2) {
		return v1 + v2;
	}
};

void test01() {
	myAdd ma;
	cout << ma(10, 10) << endl;

}
class myPrint {
public:
	myPrint() :count(0) {}
	void operator()(string test) {
		cout << test << endl;
		++count;   //函数对象可以有自己的状态  查看调用了多少次
	}
	int count;
};
void test02() {
	myPrint mp;
	mp("123");
	mp("123");
	mp("123");
	mp("123");
	cout << mp.count << endl;
}

void doPrint(myPrint& mp, string test) {    //可以作为参数传递
	mp(test);
}
void test03() {
	myPrint mp;
	doPrint(mp, "zy");
}

```
### 谓词

* 返回**bool**类型的仿函数称为**谓词**
* 如果operator()接受一个参数，那么叫做一元谓词
* 如果operator()接受两个参数，那么叫做二元谓词

如果参数列表中写的pred代表想要谓词,要放函数对象！
```
find_if  //按照条件来查找东西
```
```
class GreaterFive {
public:
	bool operator()(int val)
	{
		return val > 5;
	}
};
void test01() {
	vector<int>v;
	for (int i = 0; i < 10; ++i) {
		v.push_back(i);
	}
	vector<int>::iterator it = find_if(v.begin(), v.end(), GreaterFive());
	//GreatFive()匿名函数对象，也可以创建一个对象
	cout << *it << endl;
 }
```
#### 二元谓词

```
class Mycomare {
public:
	bool operator()(int v1, int v2) {
		return v1 > v2;
	}
};

void test01() {
	vector<int >v;
	v.push_back(34);
	v.push_back(3);
	v.push_back(4);
	v.push_back(69);
	v.push_back(1); 
	sort(v.begin(), v.end());
	for (vector<int>::iterator it = v. begin(); it != v.end(); ++it) {
		cout << *it << " ";
	}
	cout << endl;

	sort(v.begin(), v.end(), Mycomare());
	for (vector<int>::iterator it = v.begin(); it != v.end(); ++it) {
		cout << *it << " ";
	}
	cout << endl;
 }
```
### 内建函数对象

**分类:**

* 算术仿函数

* 关系仿函数

* 逻辑仿函数

**用法：**

* 这些仿函数所产生的对象，用法和一般函数完全相同
* 使用内建函数对象，需要引入头文件 `#include<functional>`

#### 算数仿函数

* 实现四则运算
* 其中negate是一元运算，其他都是二元运算
```
template<class T> T plus<T>               //加法仿函数
template<class T> T minus<T>              //减法仿函数
template<class T> T multiplies<T>         //乘法仿函数
template<class T> T divides<T>            //除法仿函数
template<class T> T modulus<T>            //取模仿函数
template<class T> T negate<T>             //取反仿函数
```
```
void test01() {
	negate<int>n;
	int a = n(50);
	cout << a << endl;

	plus<int> p;
	cout << p(10, 20) << endl;

	minus<int> m;
	cout << m(10, 20) << endl;

	multiplies<int> m2;
	cout << m2(3, 4) << endl;

	divides<int> d;
	cout << d(1, 4) << endl;

	modulus<int>m1;
	cout << m1(7, 3) << endl;
 }
```
#### 关系仿函数
关系仿函数中最常用的就是greater<>大于
```
template<class T> bool equal_to<T>                //等于
template<class T> bool not_equal_to<T>            //不等于
template<class T> bool greater<T>                 //大于
template<class T> bool greater_equal<T>           //大于等于
template<class T> bool less<T>                    //小于
template<class T> bool less_equal<T>              //小于等于
```
```
void test01() {
	vector<int>v;
	v.push_back(3);
	v.push_back(1);
	v.push_back(5);
	v.push_back(2);
	v.push_back(7);

	for (vector<int>::iterator it = v.begin(); it != v.end(); ++it) {
		cout << *it << " ";
	}
	cout << endl;
	sort(v.begin(), v.end(), greater<int>());   //注意注意
	for (vector<int>::iterator it = v.begin(); it != v.end(); ++it) {
		cout << *it << " ";
	}
	cout << endl;
 }
```
#### 逻辑仿函数

```
template<class T> bool logical_and<T>              //逻辑与
template<class T> bool logical_or<T>                //逻辑或
template<class T> bool logical_not<T>              //逻辑非
```
```


void test01() {
	vector<bool>v;
	v.push_back(true);
	v.push_back(false);
	v.push_back(true);
	v.push_back(true);
	v.push_back(false);

	for (vector<bool>::iterator it = v.begin(); it != v.end(); ++it) {
		cout << *it << " ";
	}
	cout << endl;
	//sort(v.begin(), v.end(), greater<int>());
	vector<bool>v2;
	v2.resize(v.size());                     // 搬运时目标容器要提前开辟一段空间
	transform(v.begin(), v.end(),v2.begin(), logical_not<bool>());

	for (vector<bool>::iterator it = v2.begin(); it != v2.end(); ++it) {
		cout << *it << " ";
	}
	cout << endl;
 }
```
总结：逻辑仿函数实际应用较少，了解即可

##  STL- 常用算法
算法主要是由头文件`<algorithm>` `<functional>` `<numeric>`组成
* `<algorithm>`是所有STL头文件中最大的一个，范围涉及到比较、 交换、查找、遍历操作、复制、修改等等
* `<numeric>`体积很小，只包括几个在序列上面进行简单数学运算的模板函数
* `<functional>`定义了一些模板类,用以声明函数对象。

### 常用遍历算法
```
for_each     //遍历容器
transform    //搬运容器到另一个容器中
```
#### for_each
```
for_each(iterator beg, iterator end, _func);  

// 遍历算法 遍历容器元素

// beg 开始迭代器

// end 结束迭代器

// _func 函数或者函数对象
```
```
void Print01(int val){
	cout << val+100 << " ";
}

class MyPrint {
	void operator()(int val) {
		cout << val << " ";
	}
};

void test01() {
	vector<int>v;
	for (int i = 0; i < 10; ++i) {
		v.push_back(i);
	}
	for_each(v.begin(), v.end(), Print01);  //放函数名
	cout << endl;
	for_each(v.begin(), v.end(), MyPrint()); //放函数对象
 }
```
for_each在实际开发中是最常用遍历算法，需要熟练掌握

#### transform
搬运容器到另一个容器中
```
transform(iterator beg1, iterator end1, iterator beg2, _func);
//beg1 源容器开始迭代器
//end1 源容器结束迭代器
//beg2 目标容器开始迭代器
//_func 函数或者函数对象
```
```
class Print01 {
public:
	void operator()(int val) {
		cout << val << " ";
	}
};
class MyPrint {
public:
	int operator()(int val) {
		return val;
	}
};

void test01() {
	vector<int>v;
	for (int i = 0; i < 10; ++i) {
		v.push_back(i);
	}
	for_each(v.begin(), v.end(), Print01());
	cout << endl;

	vector<int> v1;
	v1.resize(v.size());     //分配完空间全是0
	for_each(v1.begin(), v1.end(), Print01());

	transform(v.begin(), v.end(), v1.begin(), MyPrint());
	// 把函数对象取出来做一些函数操作，然后再放回去 
	//MyPrint()不需要做操作，所以就直接return
	cout << endl;
	for_each(v1.begin(), v1.end(), Print01());

 }
```
 搬运的目标容器必须要提前开辟空间，否则无法正常搬运
### 常用的查找算法
```
find             //查找元素
find_if          //按条件查找元素
adjacent_find    //查找相邻重复元素
binary_search    //二分查找法
count            //统计元素个数
count_if         //按条件统计元素个数
```
#### find
```
find(iterator beg, iterator end, value);
// 按值查找元素，找到返回指定位置迭代器，找不到返回结束迭代器位置
// beg 开始迭代器
// end 结束迭代器
// value 查找的元素
```
```
class Person {
public:
	Person(string name,int age):m_Name(name),m_Age(age) {}
	bool operator==(const Person& p1) {
		if (this->m_Name == p1.m_Name && this->m_Age == p1.m_Age) {
			return true;
		}
		return false;
	}
	string m_Name;
	int m_Age;

};

void test02() {
	vector<Person>v;
	Person p1("aaa", 10);
	Person p2("bbb", 20);
	Person p3("ccc", 30);
	Person p4("ddd", 40);

	v.push_back(p1);
	v.push_back(p2);
	v.push_back(p3);
	v.push_back(p4);

	vector<Person>::iterator it = find(v.begin(), v.end(), p2);
	if (it == v.end()) {
		cout << "没有找到" << endl;
	}
	else {
		cout << "找到了" << endl;
	}
}
```
find自定义类型的变量时，要重载==
#### find_if
按条件查找元素
```
find_if(iterator beg, iterator end, _Pred);  
// 按值查找元素，找到返回指定位置迭代器，找不到返回结束迭代器位置
// beg 开始迭代器
// end 结束迭代器
// _Pred 函数或者谓词（返回bool类型的仿函数）
```
```
class Greater20 {
public:
	bool operator()(Person& p) {
		return p.m_Age > 20;
	}
};

void test02() {
	vector<Person>v;
	Person p1("aaa", 10);
	Person p2("bbb", 20);
	Person p3("ccc", 30);
	Person p4("ddd", 40);

	v.push_back(p1);
	v.push_back(p2);
	v.push_back(p3);
	v.push_back(p4);

	vector<Person>::iterator it = find_if(v.begin(), v.end(), Greater20());
	if (it == v.end()) {
		cout << "没有找到" << endl;
	}
	else {
		cout << "找到了" << it->m_Name << " " << it->m_Age << endl;
	}
}
```
#### adjacent_find
```
adjacent_find(iterator beg, iterator end);  
// 查找相邻重复元素,返回相邻元素的第一个位置的迭代器
// beg 开始迭代器
// end 结束迭代器
```
面试题中如果出现查找相邻重复元素，记得用STL中的adjacent_find算法
```
void test01() {
	vector<int>v;
	for (int i = 0; i < 10; ++i) {
		v.push_back(i);
	}
	v.push_back(9);
	vector<int>::iterator it = adjacent_find(v.begin(), v.end());
	if (it == v.end()) {
		cout << "找不到!" << endl;
	}
	else {
		cout << "找到相邻重复元素为:" << *it << endl;
	}
 }
```
#### binary_search 
二分查找法注意: 在无序序列中不可用
```
bool binary_search(iterator beg, iterator end, value);  

// 查找指定的元素，查到 返回true  否则false
// beg 开始迭代器
// end 结束迭代器
// value 查找的元素
```
二分查找法查找效率很高，值得注意的是查找的容器中元素必须的有序序列
```
void test01() {
	vector<int>v;
	for (int i = 0; i < 10; ++i) {
		v.push_back(i);
	}
	v.push_back(9);
	bool ret = binary_search(v.begin(), v.end(),2);
	if (!ret) {
		cout << "找不到!" << endl;
	}
	else {
		cout << "找到元素为:" << endl;
	}
 }
```





































