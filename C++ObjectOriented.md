## 内存分区模型
C++在执行程序时，内存大方向划分为四个区域：代码区，全局区，栈区，堆区。

意义：不同区域存放的数据赋予不同的生命周期，更大的灵活编程。
### 程序运行前
在程序编译后，生成了exe可执行程序，未执行该程序前分为两个区域
#### 代码区
存放CPU执行的二进制机器指令。特点：共享，只读。
#### 全局区
该区域的数据在程序结束后由操作系统释放

常量：
1. 字符串常量：双引号引起来的
2. const修饰的变量：
   - const修饰的全局变量 
   - const修饰的局部变量

全局区：全局变量、静态变量（普通变量前加static 是静态变量）、常量中的：字符串常量和const 修饰的全局变量（全局常量）

不在全局区：局部变量（只要写在函数体内的变量都是局部变量）、const修饰的局部变量（局部常量）

讲义中的“其他常量”就是指const修饰的常量
### 程序运行后
#### 栈区 
由编译器自动分配释放，存放函数的参数值（形参），局部变量等。

注意：不要返回局部变量的地址，栈区开辟的数据由编译器自动释放。
```
int *func()
{	
	int a=10;
	return &a;
}
int main()
{
	int *p=func();
	cout<<*p<<endl; //第一次可能返回成功，但是还是不要
	cout<<*p<<endl;
	
	system("pause");
	return 0;
}
```

#### 堆区
由程序员分配释放， 程序结束时由操作系统回收。

C++中主要由new在堆区开辟内存 ，释放堆区的数据使用delete
```
int *func(){
int *p = new int(10); //new返回的是的是该数据类型的指针（地址），此例栈区的指针存放的堆区的地址
return p;
}
```
在堆区利用new开辟数组
```
int *p = new int(10);       //小括号创建一个变量
int *array = new int [10];  //中括号创建一个有10个变量的数组
delete[] array;             //释放数组 delete后加[] 
```
## 引用
### 基本使用 
```
int a = 10; 看到了一块内存，占用4个字节的大小，内存中存放的数据是10，想要操作内存用a
```
引用:给变量起的别名
```
int a = 10;
int &b = a;     //数据类型 &别名 = 原名;
```
### 注意事项
1. 引用必须初始化  
2. 一旦初始化后，就不可以更改了。 
```
int a = 10;
int &b = a;   // int &b;  错误的
int c = 20;
b = c;   //给b进行赋值操作，并不是b成了c的引用
```
### 引用传参
值传递：形参不会修饰实参

地址传递：形参可以修饰实参

引用传递：形参可以修饰实参  推荐，简单清楚。
```
void swap(int &a,int &b){}
swap(a,b);
```
### 引用做函数返回
1. 不要返回局部变量的引用
```
int & test1(){
  int a = 10;
  return a;
  }
  int &ref = test();   //错误，栈区的局部变量在函数执行完被释放
```
2. 如果函数的返回值是引用，函数的调用可以为左值
```
int &test2(){
  static int a = 10; //静态变量，存放在全局区，再程序结束后系统释放
  return a;
}
int &ref = test2();
test2() = 1000;
cout<<"ref = "<<ref<<endl; //输出为1000
```
### 引用的本质：指针常量
```
void func(int &ref){    //系统自动转化为int * const ref = &a;
  ref = 100;            //系统转化为*ref = 100;
}

int main(){
  int a = 10;
  int &ref = a；  //自动转化为 int * const ref = &a; 指针常量的指向不可修改。
  ref = 20;       //系统发现ref是引用自动转化为 *ref = 20;
  
  func(a);
  return 0；
}
```
### 常量引用
用来修饰**形参**，防止误操作
```
void showValue(const int&v){
  // v+=10;  使用const后防止函数中修改v导致a值改变
  cout<<v<<endl;
}

int a = 10;
//int &ref = 10;       引用必须引一块合法的内存空间可以是栈区或者堆区
int &ref = a;         //合法
const int &ref = 10;  //合法
//加上const之后 编译器将代码修改为 int temp = 10; const int &ref = temp;
//加const之后成为只读，不可修改 ref = 20; 错误

showValue(a);
```
## 函数提高
### 函数默认参数
如果自己传入了数据就用自己的数据，如果没有就用默认值。
```
// 返回值类型 函数名 （形参 = 默认值）{}
int fun01(int a, int b = 20, int c = 20){
  return a + b + c;
}
```
#### 注意事项
如果某个位置已经有了默认参数，那么从这个位置往后，从左到右都要有默认值
```
//int fun02(int a,int b = 10,int c){ 
//}
// 错误 
```
声明和实现只能有一个默认参数（二义性，编译器不知道按照声明还是按照实现）
```
//int fun02(int a = 10,int b = 10);

//int fun02(int a = 20,int b = 20){
//}
```
### 函数的占位参数
函数中形参列表里可以有占位参数（可以有初始值）用来占位，调用函数时必须填补该位置。
```
//返回值类型 函数名（数据类型）{ }
void func(int a, int){   //void func(int a,int = 10)
}


fun(10,10); //调用时必须填补占位参数
```
### 函数重载
#### 重载概述
函数名可以相同，提高复用性。需满足的条件：
- 同一个作用域下
- 函数名称相同
- 函数参数**类型不同**或者**个数不同**或者**顺序不同**

注意：函数的返回值不可以作为函数重载的条件
#### 注意事项
- 引用作为重载条件
```
void func(int &a);
void func(const int &a); //引用可以作为函数重载的条件

int a = 10;
func(a)   //调用第一个
func(10)  //调用const的 因为引用必须是一段合法的内存空间 int &a = 10错误
	  //const int &a =10  可以
//加 const 和不加const可以作为重载条件
```
- 函数重载碰到函数默认参数
```
void fun(int a,int b=10);
void fun(int a);


//func(10) 报错 出现二义性
```
建议：写函数重载不要加默认参数
## 类和对象
C++面向对象的三大特性为：**封装**、**继承**、**多态**
### 封装
意义：
- 将属性和行为作为一个整体，表现生活中的事物
- 将属性和行为加以权限控制
#### 封装意义1
在设计类的时候属性和行为写在一起，表现事物。
```
class 类名{ 访问权限:  属性 / 行为 }；
```
类: 访问权限，属性，行为

实例化：通过一个类创建一个对象的过程

类中的属性和行为统一称为**成员**
 - 属性  也称为 成员属性/成员变量
 - 行为  也称为 成员函数/成员方法
#### 封装意义2
类在设计时，可以把属性和行为放在不同的权限下，加以控制。

访问权限有三种：
- public      公共权限 成员 类内可以访问 类外可以访问
- protetcted  保护权限 成员 类内可以访问 类外不可以访问 儿子可以访问父亲的保护内容
- private     私有权限 成员 类内可以访问 类外不可以访问 儿子不可以访问父亲的私有内容

#### class和struct
唯一区别在于默认**访问权限**不同
- struct 默认权限为公共  public
- class  默认权限为私有  private
#### 成员属性私有化
优点：
- 将成员属性设置为私有，可以自己控制(set,get)读写权限
- 对于写权限，可以检测数据的有效性

通常是如果把所有的属性设置为私有，会对外提供一些public接口来对这些属性进行访问

在一个类中可以让另一个类作为本类中的成员
### 对象的初始化和清理
一个对象或者变量没有初始状态，对其使用后果是未知。

同样使用完一个对象或变量，没有及时清理也会造成一定的安全问题。

C++利用构造和析构函数解决上述问题，这两个函数会被编译器**自动调用**完成对象的初始化和清理工作。

对象的初始化和清理工作是编译器**强制**我们做的事情，因此如果我们不提供构造和析构，**编译器会提供**。编译器提供的构造函数和析构函数是**空实现**。
#### 构造函数和析构函数
构造函数：创建对象为对象成员属性赋值
```
类名 () { }
```
- 构造函数，没有返回值不写void
- 函数名称与类名相同
- 构造函数可以有参数，可以发生重载
- 创建对象的时候，程序会自动调用构造函数，无需手动调用，且只调用一次

析构函数：作用在对象销毁前，系统自动调用，执行一些清理工作。将堆区开辟的数据做释放
```
~类名() { }
```
- 析构函数：没有返回值不写void
- 函数名称与类名相同，在名称前加上符号~
- 析构函数**不可以有参数**，因此不可以发生重载
- 程序在对象销毁前会自动调用析构函数，无需手动调用，且只调用一次
#### 构造函数的分类及调用
- 按参数分为：有参构造和无参构造（默认构造）
- 按类型分为：普通构造和拷贝构造
```
class Person{
pulic:
   Person(){
      cout<<"Person的无参构造"<<endl;
   }
   Person(int a){
      age = a;
      cout<<"Person的有参构造"<<endl;
   } 
   Person(const Person &p){ // 参数写法 const 相同数据类型 &
      age = p.age;          // 将传入的p的所有属性拷贝到自己身上
   }
   
   int age;
}
// 调用
void test01(){
   //1 括号法
   Person p1;        //默认构造函数的调用
   Person p2(10)     //有参构造函数
   Person p3(p2)     //拷贝构造函数
   // 注意：调用默认构造函数时不要用括号！！！
   Person p4();
   //这一行代码编译器会认为是一个函数声明，不会认为在创建对象
   
   //2 显示法
   Person p5;
   Person p6 = Person(10); //有参构造
   Person p7 = Person(p2); //拷贝构造
   
   //Person(10) 称为匿名对象 特点：当前行执行结束后系统会立即回收掉匿名对象
   
   // 不要利用拷贝构造函数初始化匿名对象
   //Person(p3)
   //编译器会认为Person(p3) == Person p3; 对象声明无参构造，上面已经有一个p3
   
   //3 隐式转换法
   Person p8 = 10; // Person p8(10) 有参构造
   Person p9 = p8; //拷贝构造   
}
```
#### 拷贝构造函数的调用时机
- 使用一个已经创建完毕的对象来初始化一个新对象
- 值传递的方式来给函数参数传值
- 以值方式返回局部对象
```
class Person{
public:
   Person(){
      cout<<"无参构造函数"<<endl;
   }
   Person(int age){
      m_age = age;
      cout<<"有参构造函数"<<endl;
   }
   Person(const Person &p){
      cout<<"拷贝构造函数"<<endl;
      m_age = p.m_age; 
   }
   ~Person(){
      cout<<"析构函数"<<endl;
   }
   
   int m_age;
};
void tset01(){
   Person p1(10);
   Person p2(p1);    //拷贝构造
   Person p3 = p1;   //拷贝构造
}

void doWork(Person p){}
void test02(){
   Person p1;  //无参构造
   doWork(p1); //值传递的方式给函数参数传值 会调用拷贝构造函数
}

Person doWork2(){
   Person p1;
   return p1;   //以值的方式返回局部对象
}

void test03(){
   Person p = doWork2();
}
```
#### 构造函数的调用规则
默认情况下，C++编译器至少给一个类添加**3**个函数
- 默认构造函数（空实现：无参，函数体为空）
- 默认析构函数（空实现：无参，函数体为空）
- 默认拷贝构造函数，对属性进行值拷贝

构造函数的调用规则如下：
- 如果用户定义有参构造函数，C++不再提供无参构造，但是会提供默认拷贝构造
- 如果用户定义拷贝构造函数，C++不再提供其他构造函数
#### 深拷贝与浅拷贝
- 浅拷贝：简单的赋值拷贝操作
- 深拷贝：在堆区重新申请空间，进行拷贝操作

如果利用编译器提供的拷贝构造函数，会做浅拷贝操作

浅拷贝带来的问题时**堆区内存重复释放**

浅拷贝的问题利用深拷贝来解决，自己实现拷贝构造函数，在堆区开辟一块内存
```
class Person{
public:
   Person(){
      cout<<"无参构造函数！"<<endl;
   }
   Person(int age,int height){
      m_Age = age;
      m_Height = new int(height);
      cout<<"有参构造函数"<<endl;
   }
   Person(const Person &p){
      m_Age = p.m_Age;
      //m_Height = p.m_Height;  错误！编译器默认实现的浅拷贝代码
      m_Height = new int(*p.m_Height);
      cout<<"拷贝构造函数"<<endl;
   }
   ~Person(){
      if(m_Height != NULL){
         delete m_Height;
         m_Height = NULL;
      }
      //将堆区开辟的数据做释放
      cout<<"析构函数"<<endl;
   }
 
 public:
   int m_Age;
   int *m_Height;
};
void test01(){
   Person p1(10,180);
   Person p2(p1);
   cout<<p1.m_Age<<" "<<*p1.m_Height<<endl;
   cout<<p2.m_Age<<" "<<*p2.m_Height<<endl;
   // 堆区数据释放时遵循先进后出规则，当执行浅拷贝时，p2会拷贝p1的m_Height的地址
   // 析构时先析构p2将该地址释放，p1在释放该地址时报错
}

int main(){
   test01();
   system("pause");
   return 0;
}
```
如果属性有在堆区开辟的，一定要自己提供拷贝函数，防止浅拷贝带来的问题。
#### 初始化列表
c++提供初始化列表语法，来初始化属性。
```
//构造函数（）：属性1（值1），属性2（值2），...{ }
class Person{
public:
  // Person(int a,int b,int c){  传统方法
  //   m_A = a;
  //   m_B = b;
  //   m_C = c;
  //}
  Person(int a,int b,int c):m_A(a),m_B(b),m_C(c){ }
  
public:
   int m_A;
   int m_B;
   int m_C;
};

int main(){
   Person p(1,2,3);
   
   system("pause");
   return 0;
}
```
#### 类对象作为类成员
C++类中的成员可以是另一个类的对象，我们称该成员为 对象成员

当其他类对象作为本类成员，构造时先构造类对象，再构造自身，析构的顺序与构造相反。
```
class Phone{
public:
   Phone(string pName):m_pName(pName){
      cout<<"Phone构造函数"<<endl;
   }
   ~Phone(){
      cout<<"Phone析构函数"<<endl;
   }
private:
   string m_pName;
};
class Person{
public:
   Person(string name,string pName):m_Name(name),m_pName(pName){
      cout<<"构造函数"<<endl;
      
   //这里m_pName是Phone类，但是第二个输入是string
   //相当于 Phone m_pName = pName; 隐式转换法
   
   }
   ~Person(){
      cout<<"Person析构函数"<<endl;
   }

private:
   string m_Name;
   Phone m_pName;
};

void test(){
   Person p ("rng","小米");
}

int main(){
   test();
   
   system("pause");
   return 0;
}
```
#### 静态成员
静态成员就是在成员变量和成员函数前加上关键字static 称为静态成员

静态成员分为
1. 静态成员变量：
2. 静态成员函数：
##### 静态成员变量
 - 所有对象共享同一份数据
 - 在编译阶段分配内存
 - 类内声明，类外初始化

静态成员变量不属于某个对象上，所有对象都共享同一份数据。因此静态成员变量有两种访问方式：
- 通过对象进行访问
- 通过类名进行访问

非静态成员变量必须通过创建一个变量去改变那块内存

静态成员变量也**有访问权限**
```
class Person{
public:
   static int m_A;
private:
   static int m_B;
};
int Person::m_A = 100;  //类外初始化 Person::说明是Person作用域下静态成员
int Person::m_B = 200; 
void test(){
   Person p;
   cout<<p.m_A<<endl;
   Person p1;
   p1.m_A = 200;
   cout<<p.m_A<<endl;       // 通过对象进行访问
   cout<<Person::m_A<<endl; //通过类名进行访问
   // cout << Person::m_B << endl; 私有权限无法访问
}
```
##### 静态成员函数
 - 所有对象共享同一个函数
 - 静态成员函数只能访问静态成员变量
 - 也有访问权限

访问静态成员函数的两种方式：
- 通过对象访问
- 通过类名访问
```
class Person{
   static void func(){
      m_A = 100;
      // m_B = 100; 错误，不可以访问非静态成员变量 
      // 非静态成员变量必须通过构建对象来访问，无法区分到底是哪个对象的m_B
      cout<<"static void func()调用"<<endl;
   }
static int m_A;
int m_B;
};
int Person::m_A = 10;
```
### C++对象模型和this指针
#### 成员变量和成员函数分开存储
在C++中，类内的成员变量和成员函数分开存储

只有**非静态成员变量**才属于类的对象上 
```
class Person{
};
class Person1{
   int m_A;              //非静态成员变量  属于类对象上
   static int m_B;       //静态成员变量    不属于类对象上 
   void func(){ }        //非静态成员函数  不属于类对象上
   static void func2(){} //静态成员函数    不属于类对象上
};
void test01(){
   Person p;
   cout<<"size of p = "<<sizeof(p)<<endl; //空对象的时候 输出为1
   // C++编译器会给每个空对象分配一个字节的内存空间，是为了区分空对象占内存的位置
   // 每个空对象也应该有一个独一无二的地址
}

void test02(){
   Person1 p1;
   cout<<"size of p1 = "<<sizeof(p1)<<endl; //空对象的时候 输出为1
}

int main(){
   test01();
   test02();
   
   system("pause");
   return 0;
}
```
#### this 指针概念
m_  m的意思是member成员

c++提供特殊的对象指针，this指针。本质是**指针常量**

this指针指向的是被调用的成员函数所属的对象，谁调用成员函数this就指向谁

this指针是隐含每一个非静态成员内的一种指针

不需要定义，直接使用即可
```
class Person{
public:
   Person(int age){
      this->age = age; //1. 解决名称冲突
      //当形参和成员变量同名时，可用this指针区分
   }
   
   Person &PersonAddPerson(Person p){  
   // 去掉引用会返回值 则会调用拷贝构造函数 会创建一个新的对象
   // 要用引用的方式返回 会一直返回p2
      this->age+=p.age;
      return * this;
      //this 是指向p2的指针，*this就是p2的本体
   }

   int age;
};
void test01(){
   Person p1(10);
   cout<<"p1.age = "<<p1.age<<endl;
   Person p2(10);
   //链式编程思想
   p2.PersonAddPerson(p1).PersonAddPerson(p1).PersonAddPerson(p1);
	cout << "p2.age = " << p2.age << endl;
}
```
#### 空指针访问成员函数(二刷发现访问不了)
C++中空指针也可以调用成员函数，但是要注意有没有用到this指针

如果使用this指针，需要加以判断保证代码的健壮性
```
class Person{
public:
   void showClassName(){
      cout<<"Person类"<<endl;
   }
   void showPerson(){
      if(this == NULL){
         return;
      }
      // 加入这一行提高代码的健壮性
      cout<<"age = "<<m_Age<<endl;  //属性的前面默认加上了this -> m_Age
      // p作为空指针访问不到m_Age;
   }
public:
   int m_Age;
};
void test(){
   Person *p = NULL;
   p->showClassName();
   p->showPerson();
}
```
#### const 修饰成员函数
常函数：
- 成员函数后加const后我们称为这个函数为常函数
- 常函数内不可以修改成员属性
- 成员属性声明时加关键字mutable后，在常函数中依然可以更改

常对象：
- 声明对象前加const称该对象为常对象
- 常对象只能调用常函数

```
class Person{
public:
   //this 指针的本质是指针常量，指针的指向不可以修改
   // Person * const this 可以改值 不可以改指向
   void showPerson() const
   // 加入const后 const Person * const this
   //在成员函数后面加const修饰的是this,让this指向的值也不可改
   //
   {
      this -> m_B = 100; //加入关键字后就可以修改
      // m_A = 100; 等价于 this->m_A = 100;
   }
   void func(){
      m_A = 100;  //普通成员函数可以修改属性
   }
   int m_A;
   mutable int m_B;

};
void test(){
   Person p;
   p.showPerson();
}
void test1(){
   const Person p; //在对象前加const，变为常对象
   // p.m_A = 100; // 报错
   p.m_B = 100;   //正确 m_B特殊值 
   //不管是常对象还是常函数，加了mutable的属性都可以修改
   p.showPerson(); //常对象只能调用常函数
   // p.func();    //不可以调用普通的成员函数，普通成员函数可以修改属性
}
```
### 友元（friend）
在程序里，有些私有属性 也想让类外特殊的一些函数或者类进行访问，就需要用到友元的技术

友元的目的就是让一个函数或者类 访问另一个类中私有成员

三种实现:
- 全局函数做友元
- 类做友元
- 成员函数做友元

#### 全局函数做友元
全局函数调用类的私有属性
```
class 类名{
   friend + 函数声明;
}
```
实例
```
class Building{

friend void goodGay(Building *building);
// 全局函数放到类中
public:
   Building(){
      m_SittingRoom = "客厅";
      m_BeddingRoom = "卧室";	
   }	
public:
   string m_SittingRoom;
private:
   string m_BeddingRoom;
};
void goodGay(Building *building){
   cout<<"好基友正在访问"<<building->m_SittingRoom<<endl;
   cout<<"好基友正在访问"<<building->m_BeddingRoom<<endl;
}
int main(){
   Building b;
   goodGay(&b);
   
   system("pause");
   return 0;
}
```
#### 类做友元
一个类可以访问另一个类的私有内容
```
class 类1{
   friend class 类2;
   // 告诉编译器，类2是类1的好朋友，可以访问类1的私有内容
};
```
#### 成员函数做友元
```
class 类1{
   friend 返回类型 类2::函数名(参数值);
   //告诉编译器，类2中的成员函数可以访问类1的私有内容
}
```
注意：因为GoodGay中定义b要用到Building，Building要声明在GoodGay的前面。

GoodGay::visit1()访问需要用到友元，就需要友元在visit01()前。

Building要想知道GoodGay哪个函数是自己的友元需要GoodGay定义在Building前面。

所以需要分开定义visit01类内定义，类外实现。

（二刷发现其实也未必就这样，提前声明好就好了）

```
定义创建了对象并且分配了内存，声明没有分配内存。
```
```
class Building;
class GoodGay {
public:
	GoodGay();
	void visit1();
	void visit2();
private:
	Building* b;
};

class Building {
	friend void GoodGay::visit1();
private:
	string BedRoom;
public:
	string SittingRoom;
public:
	Building():BedRoom("卧室"), SittingRoom("客厅") {}
};

GoodGay::GoodGay()
{
	b = new Building;
}

void GoodGay::visit1() {
	cout << b->SittingRoom << endl;
	cout << b->BedRoom << endl;
}

void GoodGay::visit2() {
	cout << b->SittingRoom << endl;
	//cout << b->BedRoom << endl;
}

void test01()
{
	GoodGay  g;
	g.visit1();
}

int main() {
	test01();

	system("pause");
	return 0;
}
```
### 运算符重载
对已有的运算符重新进行定义，赋予其另一种功能，以适应不同的数据类型,也可以发生运算符重载
#### 加号运算符重载
```
返回类型 operator+(参数值){}
```
实现两个自定义数据类型相加的运算（对于内置数据类型，编译器知道如何进行运算）
- 成员函数重载+
- 全局函数重载+
```
class Person{
public:
   int m_A;
   int m_B;
public:
   Person();
   Person(int A,int B):m_A(A),m_B(B){}
   
   Person operator+(const Person &p1){
      Person temp(0,0);
      temp.m_A = m_A + p1.m_A;
      temp.m_B = m_B + p1.m_B;
      return temp;
   }
};

//Person operator+(const Person &p1,const Person&p2){
//   Person temp(0,0);
//   temp.m_A = p1.m_A + p2.m_A;
//   temp.m_B = p1.m_B + p2.m_B;
//   return temp;
//}

void test01(){
   Person p1(10,20);
   Person p2(30,40);
   Person p3 = p1 + p2;
   cout<<p3.m_A<<p3.m_B<<endl;
}
int main(){
   test01();
   
   system("pause");
   return 0;
}

```
- 对于内置的数据类型的表达式的的运算符是不可能改变的
- 不要滥用运算符重载
#### 左移运算符
可以输出自定义数据类型
```
operator<<
```
不会利用成员函数重载<<运算符，因为无法实现cout在左侧，只能利用全局函数重载
```
class Person{
friend  ostream& operator<<(ostream& cout, Person p);
private:
   int m_A;
   int m_B;
public:
   Person(int a,int b):m_A(a),m_B(b){}
};
// 如果一个函数不知道返回什么东西 可以先用void返回
ostream& operator<<(ostream& cout,Person p){
   cout<<"m_A = "<<p.m_A<<" m_B = "<<p.m_B;
   return cout;
}

//cout：标准的输出流对象ostream，全局只能有一个 传递需要用引用
void test01(){
   Person p(10,10);
   cout<<p<<endl;
}

int main(){
   test01();

   system("pause");
   return 0;
}
```
重载左移运算符配合友元可以实现输出自定义数据类型
#### 递增运算符重载
通过重载递增运算符，实现自己的整型数据
- 重载前置++运算符
- 重载后置++运算符
```
class MyInteger{
friend ostream & operator<<(ostream& cout,MyInteger myint);
public:
   MyInteger():m_Num(0){}
   //前置递增
   MyInteger& operator++(){
   //不需要传参数
   // 返回引用是为了一直对一个数据进行递增操作
      ++m_Num;
      return *this;
      //将对象自身做一个返回
   }
   
   //后置递增 
   MyInteger operator++(int){
   // int 代表占位参数，可以用于区分前置递增和后置递增
      MyInteger temp = *this;
      m_Num++;
      return temp;
   
   }
   
private:
   int m_Num;
};

ostream & operator<<(ostream& cout,MyInteger myint){
   cout<<myint.m_Num;
   return cout;
}

void test01(){
   MyInteger myInt;
   cout<<++myInt<<endl;
}

void test02(){
   MyInteger myInt;
   cout << myInt++ << endl;
   cout << myInt << endl;
}
```
前置递增返回引用，后置递增返回值
#### 赋值运算符重载
c++编译器至少给一个类添加4个函数
1. 默认构造函数(无参，函数体为空)
2. 默认析构函数(无参，函数体为空)
3. 默认拷贝构造函数，对属性进行值拷贝
4. 赋值运算符 operator=, 对属性进行值拷贝

如果类中有属性指向堆区，做赋值操作时也会出现深浅拷贝问题
```
class Person{
public:
   int * m_Age;
   Person(int age){
      m_Age = new int(age); 
   }
   ~Person(){
      if(m_Age != NULL){
         delete m_Age;
	 m_Age = NULL;
      }
   }
   Person& operator=(Person &p){
      //编译器提供的浅拷贝 
      // m_Age = p.m_Age;
      
      //应先判断是否有属性在堆区，如果有，先释放干净，然后再深拷贝
      if(m_Age != NULL){
         delete m_Age;
	 m_Age = NULL;
      }
      m_Age = new int(*p.m_Age);
      return *this;
   }
   //返回引用为了可以连等   
};
void test01(){
   Person p1(18);
   Person p2(20);
   Person p3(30);
   p3 = p2 = p1;
   cout<<"p1的年龄为："<<*p1.m_Age<<endl;
   cout<<"p2的年龄为："<<*p2.m_Age<<endl;
   cout<<"p3的年龄为："<<*p3.m_Age<<endl;
}
```
二刷有问题的代码
```
class Person {
public:
	Person(int age) {
		m_Age = new int(age);
	}
	Person(const Person& p) {
		cout << "拷贝构造函数" << endl;
		if (m_Age != nullptr) {
			delete m_Age;
			m_Age = nullptr;
		}
		m_Age = new int(*p.m_Age);
	}
	Person &operator=(Person p) {      //；应该是拷贝构造时除了问题，将这里变成引用就不报错了
		if (this->m_Age != nullptr) {
			delete this->m_Age;
			m_Age = nullptr;
		}
		this->m_Age = new int(*p.m_Age);
		return *this;
	}

	~Person() {
		if (m_Age != nullptr) {
			delete m_Age;
			m_Age = nullptr;
		}
	}

public:
	int* m_Age;

};

void test01() {
	Person p1(20);
	Person p2(18);
	p2 = p1;
};
```
#### 关系运算符重载
重载关系运算符，可以让两个自定义类型对象进行对比操作
```
class Person{
public:
   Person(string name, int age):m_Name(name),m_Age(age){}
   
   string m_Name;
   int m_Age;
   bool operator==(const Person &p2){
      if((m_Name == p2.m_Name)&&(m_Age == p2.m_Age)){
         return true;
      }
      return false;
   }
   bool operator!=(const Person &p2){
      if((m_Name == p2.m_Name)&&(m_Age == p2.m_Age)){
         return false;
      }
      return true;
   }
};

void test01(){
   Person p1("Tom",18);
   Person p2("Tom",18);
   
    if (p1 == p2) {
        cout << "p1与p2是相等的" << endl;
    }
    else {
        cout << "p1与p2是不相等的" << endl;
    }
     if (p1 != p2) {
        cout << "p1与p2是不相等的" << endl;
    }
    else {
        cout << "p1与p2是相等的" << endl;
    }
}
```
#### 函数调用运算符重载
* 函数调用运算符 ()  也可以重载
* 由于重载后使用的方式非常像函数的调用，因此称为**仿函数**
* 仿函数没有固定写法，非常灵活

重载小括号就叫仿函数
```
class MyPrint{
public:
   void operator()(string test){
      cout<<test<<endl;
   }

};
void MyPrint02(string test){
   cout<<test<<endl;
}

class MyAdd{
public:
   int operator()(int num1,int num2){
      return num1 + num2;
   }
};

void test01(){
   MyPrint m;
   m("helloworld");
   MyPrint02("hi");
}

void test02(){
   MyAdd myAdd;
   int ret = myAdd(100,10);
   cout<<"ret = "<<ret<<endl;
   
   // 匿名函数对象 匿名对象重载了小括号()
   // 类型+() 就是匿名对象 当前行执行完立刻被释放 
   // 
   cout<<MyAdd()(10,100)<<endl;
}
```
### 继承
减少代码量
#### 基本语法
```
class 子类:继承方式 父类
```
- 子类 也称为 派生类
- 父类 也称为 基类

派生类中的成员包含两大部分：
一类是从基类继承过来的，一类是自己增加的成员。从基类继承过过来的表现其共性，而新增的成员体现了其个性。
#### 继承方式
- 公共继承
- 保护继承
- 私有继承

父类中的私有内容子类无论哪种继承方式都无法访问
![image](https://github.com/iyi111111/CPPLearning/blob/main/cppimage/%E7%BB%A7%E6%89%BF.png)
#### 继承中的对象模型
```
class Base{
public:
   int m_A;
protected:
   int m_B;
private:
   int m_C;
};

class Son:public Base{
public:
   int m_D;

};
void test01(){
   Son son1;
   cout<<sizeof(son1)<<endl;
   //父类中所有的非静态成员属性都会被子类继承下去
   //父类中的私有成员属性是被编译器隐藏了，因此访问不到，但是确实被继承了
}
```
#### 继承中构造和析构顺序
继承中 先调用父类构造函数，再调用子类构造函数，析构顺序与构造相反
#### 继承同名成员处理方式
* 访问子类同名成员   直接访问即可
* 访问父类同名成员   需要加作用域

**如果子类中出现和父类同名的成员函数，子类的同名成员会隐藏掉父类中所有的同名成员函数。如果想访问父类中被隐藏的同名成员函数，需要加作用域**
```
son.Base::m_A;
son.Base::func();
son.Base::func(100);
```
#### 继承同名静态成员处理方式
静态成员和非静态成员出现同名，处理方式一致

- 访问子类同名成员   直接访问即可
- 访问父类同名成员   需要加作用域
```
class Base{
public:
   static int m_A;
   static void func(){
      cout<<"Base"<<endl;
   }
};
int Base::m_A = 100;
class Son:public Base{

public:
  static int m_A;
  static void func(){
      cout<<"Son"<<endl;
   }
};
int Son::m_A = 200;

void test01(){
   Son son1;
   // 通过对象访问
   cout<<son1.m_A<<endl;
   cout<<son1.Base::m_A<<endl;
   // 通过类名访问
   cout<<Son::m_A<<endl;
   cout<<Base::m_A<<endl;
   cout<<Son::Base::m_A<<endl;
   // 通过子类对象访问到父类对象 通过类名
   // 第一个:: 代表通过类名的方式访问
   // 第二个:: 代表访问父类作用域下
}
void test02(){
   Son s;
   //通过对象访问
   s.func();
   s.Base::func();
   //通过类名访问
   Son::func();
   Son::Base::func();
   //
}
```
####  多继承语法
C++允许**一个类继承多个类**
```
class 子类: 继承方式 父类1, 继承方式 父类2...
```
多继承可能会引发父类中有同名成员出现，需要加作用域区分

C++实际开发中不建议用多继承
#### 菱形继承

两个派生类继承同一个基类

又有某个类同时继承这两个派生类

这种继承被称为菱形继承，或者钻石继承
```
class Animal{
public:
   int m_Age;
};
// 利用虚继承解决菱形继承问题
// 继承之前 加上关键字virtual 变为虚继承
// Animal类称为 虚基类

class Sheep:virtual public Animal{};
class Tuo:virtual public Animal{};
class SheepTuo:public Sheep,public Tuo{};

void test01(){
   SheepTuo st;
   
   st.Sheep::m_Age = 18;
   st.Tuo::m_Age = 28;
   // 菱形继承，当两个父类拥有相同的数据，需要加作用域区分
   cout<<"Sheep "<<st.Sheep::m_Age <<endl;
   cout<<"Tuo "<<st.Tuo::m_Age <<endl;
   // 菱形继承导致两份分数 但是SheepTuo只用有一份数据就可以，造成了资源的浪费
   cout << "st.m_Age = " << st.m_Age << endl;
}
```
vbptr 虚基类指针 指向vbtable 虚基类表 继承两个指针

v - virtual 

b - base

ptr - pointer
并不是继承的两份数据，而是继承的两个指针，两个指针会通过偏移量找到唯一的数据，这个数据只有一个。上面一开始是18，下面改成28了，所以访问都是28
代码很少使用

## 多态（父类指针指向子类对象）
### 多态的基本概念

多态分为两类

* 静态多态: 函数重载 和 运算符重载属于静态多态，复用函数名
* 动态多态: 派生类和虚函数实现运行时多态

静态多态和动态多态区别：

* 静态多态的函数地址早绑定  -  编译阶段确定函数地址
* 动态多态的函数地址晚绑定  -  运行阶段确定函数地址

C++中允许父子之间的类型转换，不需要强制类型转换，父类的引用可以直接指向子类的对象。

区别重载和重写
- 重载： 函数名相同，参数不一样
- 重写： 函数的返回值类型、函数名、形参列表中的所有内容要相同
```

class Animal{
public:
   virtual void Speak(){
      cout<<"动物在说话"<<endl;
   }
};

class Cat:public Animal{
public:
// 虚函数
   void Speak(){
      cout<<"小猫在说话"<<endl;
   }
};

class Dog:public Animal{
public:
   void speak(){
      cout<<"小狗在说话"<<endl;
   }
}


// 地址早绑定 在编译阶段确定函数地址
// 如果想让猫说话，那么函数地址就不能提前绑定，需要在运行阶段绑定 地址晚绑定

// 动态多态的满足条件
// 子类要继承父类
// 子类要重写父类的函数

// 动态多态的使用
// 父类的指针或者引用指向子类对象

void doSpeak(Animal &animal){
// Animal &animal = cat;

public:
   animal.Speak();
}

void test01(){
   Cat cat;
   doSpeak(cat);
}

```
子类重写父类的虚函数时，子类的函数处的virtual可写可不写，父类肯定要写
#### 多态原理剖析
animal内部结构：

vfptr 虚函数(表) v virtual  f function  ptr  pointer ：指针会指向一个虚函数表

vftable 虚函数表（记录表内虚函数的地址） v virtual  f function  t table：&Animal::speak(成员函数的函数地址要加上作用域)

cat的内部结构（未重写）：
当只有继承时，子类全部继承父类的，子类的vftable中也是 

vfptr 继承一个指针

vftable 构建子类的虚函数表 ：&Animal::speak

当子类重写父类的虚函数时，父类不变，子类中的虚函数表内部会替换成子类的虚函数地址：&Cat::speak

当父类的指针或者引用指向子类对象时，发生多态
```
Animal  &animal = cat;
animal.speak();
```
animal调用speak时，由于指向的是cat对象，所以会从cat的虚函数表中找这个函数

#### 多态案例 —— 计算器类
多态的优点：
- 代码组织结构清晰
- 可读性强
- 利于前期和后期扩展以及维护
```
class Calculator {
public:
    int getResult(string oper) {
        if (oper == "+") {
            return m_Num1 + m_Num2;
        }
        else if (oper == "-") {
            return m_Num1 - m_Num2;
        }
        else if (oper == "*") {
            return m_Num1 * m_Num2;
        }
    }
public:
    int m_Num1;
    int m_Num2;
};

void test01() {
    Calculator c;
    c.m_Num1 = 2;
    c.m_Num2 = 4;
    cout << c.getResult("+") << endl;
    cout << c.getResult("-") << endl;
    cout << c.getResult("*") << endl;
}

class AbstractCalculator {
public:

    virtual int getResult() {
        return 0;
    }
public:
    int m_Num1;
    int m_Num2;
};

class AddCalculator:public AbstractCalculator {
public:
    int getResult() {
        return m_Num1 + m_Num2;
    }
};

class SubCalculator :public AbstractCalculator {
public:
    int getResult() {
        return m_Num1 - m_Num2;
    }
};
class MulCalculator :public AbstractCalculator {
public:
    int getResult() {
        return m_Num1 * m_Num2;
    }
};

void test02() {
    AbstractCalculator* a = new AddCalculator;
    a->m_Num1 = 20;
    a->m_Num2 = 19;
    cout << a->getResult() << endl;
    delete a;

    a = new  SubCalculator;
    a->m_Num1 = 20;
    a->m_Num2 = 19;
    cout << a->getResult() << endl;
    delete a;

    a = new MulCalculator;
    a->m_Num1 = 20;
    a->m_Num2 = 19;
    cout << a->getResult() << endl;
    delete a;
 
}
```
#### 纯虚函数和抽象类

在多态中，通常父类中虚函数的实现是毫无意义的，主要都是调用子类重写的内容

因此可以将虚函数改为**纯虚函数**

纯虚函数语法：
```
virtual 返回值类型 函数名 (参数列表) = 0 ；
```

当类中有了纯虚函数（有一个就叫），这个类也称为==抽象类==

**抽象类特点**：

 * 无法实例化对象
 * 子类必须重写抽象类中的纯虚函数，否则也属于抽象类

```
class Base{
public:
   virtual void func()=0;
};

class Son : public Base{
public:
   void func(){
      cout<<"Son调用func"<<endl;
   }
};

void test01(){
   Base *base = new Son;
   base->func();
   delete base;
}

```
#### 多态案例2
```
class AbstractDrinking {
public:
	virtual void Boil() = 0;
	virtual void Brew() = 0;
	virtual void PourInCup() = 0;
	virtual void PutSomething() = 0;

	void MakeDrink() {
		Boil();
		Brew();
		PourInCup();
		PutSomething();
	}
 };

class Coffee : public AbstractDrinking {
public:
	void Boil() {
		cout << "煮农夫山泉" << endl;
	}
	void Brew() {
		cout << "冲泡咖啡" << endl;
	}
	void PourInCup() {
		cout << "倒入杯中" << endl;
	}
	void PutSomething() {
		cout << "加入一些东西" << endl;
	}
};

class Tea : public AbstractDrinking {
public:
	void Boil() {
		cout << "煮矿泉水" << endl;
	}
	void Brew() {
		cout << "冲泡茶水" << endl;
	}
	void PourInCup() {
		cout << "倒入杯中" << endl;
	}
	void PutSomething() {
		cout << "加入一些东西" << endl;
	}
};

void doWork(AbstractDrinking* abs) {
	abs->MakeDrink();
	delete abs;
}

void test01() {
	doWork(new Coffee);
	cout << "--------------------------" << endl;
	doWork(new Tea);
}
```
#### 虚析构和纯虚析构
多态使用时，如果子类中有属性开辟到堆区，那么父类指针在释放时无法调用到子类的析构代码

解决方式：将父类中的析构函数改为**虚析构**或者**纯虚析构**

虚析构和纯虚析构共性：

* 可以解决父类指针释放子类对象
* 都需要有具体的函数实现

虚析构和纯虚析构区别：

* 如果是纯虚析构，该类属于抽象类，无法实例化对象

虚析构：
```
virtual ~类名(){}
```
纯虚析构（需要声明也需要实现）：
```
virtual ~类名() = 0;
类名::~类名(){}
```
纯虚函数等于0就完事了不需要任何实现。
```
// 父类指针在析构的时候不会调用子类中的析构函数，导致子类如果有堆区属性，出现内存泄漏
```
- 虚析构或纯虚析构就是用来解决通过父类指针释放子类对象
- 如果子类中没有堆区数据，可以不写为虚析构或纯虚析构
- 拥有纯虚析构函数的类也属于抽象类
```
class Animal {
public:
	Animal() {
		cout << "animal的构造函数" << endl;
	}
	//virtual ~Animal() {
	//	cout << "animal的析构函数" << endl;
	//}
	//纯虚析构 需要声明也需要实现
	virtual ~Animal() = 0;
	virtual void Speak() = 0;
};
Animal::~Animal() {
	cout << "animal的析构函数" << endl;
}

class Cat: public Animal {
public:
	Cat(string name) {
		m_Name = new string(name);
		cout << "Cat的构造函数" << endl;
	}
	~Cat() {
		if (m_Name != NULL) {
			delete m_Name;
			m_Name = NULL;
		}
		cout << "Cat的析构函数" << endl;
	}
	void Speak() {
		cout << *m_Name << "在说话" << endl;
	}

public:
	string* m_Name;
};

void test01() {
	Animal* abs = new Cat("TGo");
	abs->Speak();
	delete abs;
}
```
#### 案例三电脑组装
```
class CPU {
public:
	virtual void calculator() = 0;
};
class VideoCard {
public:
	virtual void display() = 0;
};
class Memory {
public:
	virtual void storage() = 0;
};

class Computer {
public:
	Computer(CPU* cpu, VideoCard* vc, Memory* mem) {
		m_Cpu = cpu;
		m_Vc = vc;
		m_Mem = mem;
	}

	~Computer() {
		if (m_Cpu != NULL) {
			delete m_Cpu;
			m_Cpu = NULL;
		}
		if (m_Vc != NULL) {
			delete m_Vc;
			m_Vc = NULL;
		}
		if (m_Mem != NULL) {
			delete m_Mem;
			m_Mem = NULL;
		}
		cout << "computer析构已经调用" << endl;
	}

	void doWork() {
		m_Cpu->calculator();
		m_Vc->display();
		m_Mem->storage();
	}

private:
	CPU* m_Cpu;
	VideoCard* m_Vc;
	Memory* m_Mem;
};


class InterCpu : public CPU {
public:
	virtual void calculator() {
		cout << "Iner的CPU开始计算了" << endl;
	}
};
class InterVideoCard : public VideoCard {
public:
	void display() {
		cout << "Iner的显卡开始显示了" << endl;
	}
};

class InterMemory : public Memory {
public:
	virtual void storage() {
		cout << "Iner的内存开始存储了" << endl;
	}
};

class LenovoCpu : public CPU {
public:
	virtual void calculator()
	{
		cout << "Lenovo的CPU开始计算了！" << endl;
	}
};
class LenovoVideoCard : public VideoCard {
public:
	virtual void display()
	{
		cout << "Lenovo的显卡开始显示了！" << endl;
	}

};

class LenovoMemory : public Memory {
public:
	virtual void storage()
	{
		cout << "Lenovo的内存条开始存储了！" << endl;
	}
};

void test01() {
	CPU* intercpu = new InterCpu;
	VideoCard* intervs = new InterVideoCard;
	Memory* intermem = new InterMemory;
	Computer* computer1 = new Computer(intercpu, intervs, intermem);
	computer1->doWork();
	//delete computer1;
	cout << "--------------------" << endl;

	CPU* lenovocpu = new LenovoCpu;
	VideoCard* lenovovs = new LenovoVideoCard;
	Memory* lenovomem = new LenovoMemory;
	Computer compuer2(lenovocpu, intervs, intermem);
	compuer2.doWork();

	cout << "--------------------" << endl;
	Computer* computer3 = new Computer(new LenovoCpu, new LenovoVideoCard, new InterMemory);
	computer3->doWork();

}
```
## 文件操作
程序运行时产生的数据都属于临时数据，程序一旦运行结束都会被释放

通过**文件可以将数据持久化**

C++中对文件操作需要包含头文件
```
<fstream>
```
文件类型分为两种：

1. **文本文件**     -  文件以文本的**ASCII码**形式存储在计算机中
2. **二进制文件** -  文件以文本的**二进制**形式存储在计算机中，用户一般不能直接读懂它们

操作文件的三大类:

1. ofstream：写操作
2. ifstream： 读操作
3. fstream ： 读写操作
### 文本文件
#### 写文件
1.包含头文件
```
#include<fstream>
```
2.创建流对象
```
ofstream ofs;
```
3.打开文件
```
ofs.open("文件路径",打开方式);
```
4.写入数据
```
ofs<<"写入的数据";  //文件的输出流对象
```
5.关闭文件
```
ofs.close();
```
文件打开方式：
```
ios::in      //为读文件而打开文件
ios::out     //为写文件而打开文件
ios::ate     //初始位置：文件尾
ios::app     //追加方式写文件
ios::trunc   //如果文件存在先删除，再创建
ios::binary  //二进制方式
```
**注意**:文件打开方式可以配合使用，利用 | 操作符。
```
ios::binary | ios::out;
```
* 文件操作必须包含头文件 fstream
* 读文件可以利用 ofstream  ，或者fstream类
* 打开文件时候需要指定操作文件的路径，以及打开方式
* 利用<<可以向文件中写数据
* 操作完毕，要关闭文件
#### 读文件
读文件与写文件步骤相似，但是读取方式相对于比较多

1.包含头文件
```
#include<fstream>
```
2.创建流对象
```
ifstream ifs;
```
3.打开文件，并判断文件是否打开成功
```
ifs.open("文件路径",打开方式);
```
4.读数据
四种方式读取

5.关闭文件
```
ifs.close();
```
```
void test01() {
	ifstream ifs;
	ifs.open("test.txt", ios::in);
	if (!ifs.is_open()) {
		cout << "文件打开失败" << endl;
		return;
	}
	//char buf[1024] = { 0 };
	//while (ifs >> buf) {
	//	cout << buf << endl;
	//}

	//char buf[1024] = { 0 };
	//cout << sizeof(buf) << endl;
	//while (ifs.getline(buf, sizeof(buf))) {
	//	cout << buf << endl;
	//}

	string buf;
	while (getline(ifs, buf)) {
		cout << buf << endl;
	}
	ifs.close();
}
```
- 读文件可以利用 ifstream  ，或者fstream类
- 利用is_open函数可以判断文件是否打开成功
- close 关闭文件 
### 二进制文件
以二进制的方式对文件进行读写操作

打开方式要指定为
```
ios::binary
```
#### 写文件
二进制方式写文件主要利用流对象调用成员函数write

函数原型 ：
```
ostream& write(const char*buffer,int len);
```
参数解释：字符指针buffer指向内存中一段存储空间。len是读写的字节数

**对文件进行操作时，最好用C风格字符串**
```
class Person {
public:
	char m_Name[64];  //对文件进行操作时，用C风格字符串
	int m_Age;
};

void test01() {
	ofstream ofs("Person.txt", ios::out | ios::binary);
	Person p = { "xiaoyi",3 };     
	// 这里还必须这样初始化哦，c++提供的默认构造函数是空实现，另一个提供的是拷贝构造。
	ofs.write((const char*)&p,sizeof(p));
	ofs.close();
}
```
#### 读文件
二进制方式读文件主要利用流对象调用成员函数read

函数原型：
```
istream &read(char *buffer,int len);
```
参数解释：字符指针buffer指向内存中一段存储空间。len是读写的字节数
```
class Person {
public:
	char m_Name[64];
	int m_Age;
};

void test01() {
	ifstream ifs("Person.txt", ios::in | ios::binary);
	Person p ;
	ifs.read((char*)&p,sizeof(p));
	cout << p.m_Name << p.m_Age << endl;
	ifs.close();
}
```

文件输入流对象 可以通过read函数，以二进制方式读数据
