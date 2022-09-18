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






































