# QA summary

- 操作符endl作用：输出换行符并刷新输出流
- Hex操纵符将整数输出的进制设置为十六进制
- Scientific用于设置浮点数为科学计数法格式
- C++命名空间作用：避免全局标识符的名字冲突
- 对于以下代码：
  ```cpp
  #include<iostream>
  using namespace std;
  int main(){
    int cout;
    cin>>cout;
    std::cout<<(cout<<cout);
  }
  ```
  输入为2，则输出为8：cout值为2，cout << cout相当于 2<<2，相当于二进制左移2位（10 -> 1000），即十进制的8
- 引用必须被定义，且不能为空
- Fixed默认小数点后六位
- substr(起始位置，字串长度)：取从起始位置起长度为字串长度的子串
- find()找不到字串时返回 std::npos
- 正确追加字符：s+ = 'a'
- 执行clear()后，capacity()值保持不变
- compare(s1,s2)：返回0，说明s1==s2；返回-1，说明字典序s1 < s2；返回1，字典序s1 > s2
- insert函数的重载形式合法的是：s.insert(2,5,'X')
- 编译时常量constexpr

## 数据抽象与类

- 类成员public private protected声明顺序任意
- 调用类的成员函数：对象名.成员函数名 或者 类名::静态成员函数名
- 静态成员必须在类外进行初始化，只能访问静态成员变量（非静态都可访问）
- 静态成员属于类本身，非静态成员属于对象
- 静态成员的作用域为整个程序
- 一个类可以有任意个构造函数但只能有一个析构函数
- {{}}嵌套，内部的{}构成一个局部作用域，内部元素在{}内构造和析构，不在最终析构
- 构造函数不能有返回值类型，可以重载，可以有默认参数
- 静态成员函数不能访问this指针（类级别操作），静态成员变量可以是const
- static局部变量只初始化一次，生命周期从第一次初始化开始到程序结束，在函数调用途中不会析构
- 对于以下代码：
  ```cpp
  #include<iostream>
  using namespace std;
  class A{
    public:
        static int count;
        A(){ count++;}
        ~A(){count--;}
  };

  int A::count=0;
  int main(){
    A a1,a2;
    cout<<A::count<<endl;>>
  }  
  ```
  输出为2：经过a1,a2两次构造，但是在输出A::count时程序还未结束，只有当程序结束才析构，故输出2，未经过析构环节
- 在C中，动态分配使用malloc(),C++中推荐使用new delete 来替代malloc与free，因为new delete可以自动调用构造与析构
- 堆内存时动态的，栈内存大小在编译时确定是固定不变的
- 堆可能产生内存碎片
- 使用new 分配数组时：new a[n]构造N次，delete []a析构N次
- new 分配单个对象时：new A构造1次，delete析构1次
- 当new[]与delete不匹配时崩溃（new数组，delete却没带[]）
- 当new-delete后再调用new指针会抛出garbage value
- 构造函数的初始化列表的初始化优先级比默认成员初始化器优先级高
- new创建的类实例在堆区，引用保存在栈区
- 实现深拷贝（手动管理资源）必须自定义：拷贝构造函数 析构函数 拷贝赋值运算符
- 一个类只能定义一个拷贝构造函数 classname(const classname& other){}

### note
- 类不声明访问说明符默认private
- 私有成员只有成员函数与友元函数可以访问
- static不用this,用类名::成员
- static变量类内声明，类外定义
- 静态成员提供类级别操作（无需指针），非静态成员提供对象级操作（依赖指针）
  
- 同名函数不同返回类型不可触发重载

- new运算返回该类型指针指向分配到的内存空间，若内存分配失败抛出异常
- 用delete释放空间后，指针的值仍是原来指向的地址（不修改指针的值）但指针失效（内存不可用）

- 联编：静态：**编译阶段**确定函数调用，**无法实现多态**
- 动态：**运行阶段**通过**虚函数**实现确定函数调用

=======
## 运算符重载

- C++不允许内置类型的运算符（如int+int行为不能重载，只能重载类类型）
- 重载运算符时运算符目数固定，不能变（单目 -> 双目×）
- 重载赋值运算符时通常返回当前对象的引用：
    支持链式赋值，避免不必要的拷贝，与内置行为一致

【判断返回值类型】
- 是否需要链式操作：是 -> 返回引用（=，+=）不是 ->可以返回void(<)
- 是否需要避免拷贝：是 ->返回引用 不是 -> 可以返回副本
  
- 当同名函数：非const对象优先调用非const函数，const版本不被调用
- 从自身可修改性，对数据的访问权限等角度考虑a特性：
    1.int& a = b 相当于 const int* a = &b × 引用不可重新绑定，可修改数据；指针相反 
    2.const int& a = b 相当于 const int* const a = &b√均不可绑定，均不可通过a改目标数据
- 函数对象是一个对象但是使用形式看起来像函数调用，实际上也执行函数调用

### note
- **不可重载运算符**
    ::  作用域解析							
    .   成员访问						 
    ?:  三元条件
    .*  通过成员指针的成员访问		 
    sizeof（除new delete外的）关键字运算符
    '#'   预处理符号 						
    自创的新运算符

- 友元函数**不属于类**，可以访问类的变量及函数（包括私有变量） **需要在类外实现**

**选择：成员函数重载 or 友元函数重载**
|运算符类型	|重载方式	|理由|
|--|--|--|
|赋值 += -=|	成员函数	|需要修改对象状态，左操作数必须是当前类对象|
|下标[]	|成员函数	|必须作用于对象|
|流操作 << >>	|友元函数	|左操作数是流对象（如cout）无法用成员函数实现|
|算数对称运算+ -	|友元函数|	支持a+b a-b的对称性|
|比较 == <	|友元函数	|通常不修改对象状态，可能需要访问双方私有成员|


## 继承与派生
- 使用using声明可以改变基类成员在派生类的访问权限
- 友元关系不可继承：基类的友元不会自动成为派生类的友元；派生类的友元也不自动成为基类友元；友元关系是非传递的支队声明它的类有效
- protected可以访问基类的public,不能访问基类的private
- 构造函数顺序：从基到派，从左到右，class Z： public Y,public X{Z()} 构造顺序：Y,X,Z
- 派生类构造拷贝函数时：先积累拷贝构造，后派生类拷贝构造
- 基类构造函数为protected,基类无法直接对象实例化，派生类不必须显式调用基类构造函数
- 当基类没有默认构造函数或需要传递参数时才需要显式调用

- 对于以下代码：
  ```cpp
  class A{
    public:
        A(){cout<<"A";}
        ~A(){cout<<"~A";}
  };
  class B: public A{
    public:
        B(){cout<<"B";}
        ~B(){cout<<"~B";}
  };
  int main(){
    A* p=new B();
    delete p;
  }
  ```
- 输出为AB~A：new B()调用A构造函数，再调用B构造函数；delete p由于p是A*且A的析构函数**不是虚函数**，因此只会调用A的析构函数，不会调用B的，导致派生类部分资源未被释放，内存泄漏

- 如果一个类继承自非虚类，且没有使用作用域解析运算符，那么该类实例对象上访问同名成员时会自动调用派生类成员
- 构造函数调用顺序：虚函数 -> 非虚函数 基类 -> 派生 左 -> 右
- 菱形继承问题通常通过虚继承解决
- 若一个派生类和它的非虚基类有同名的成员函数，可以使用基类名+作用域解析运算符直接调用基类的成员函数

**指针const的兼容性**：
非const-> const：安全，T*可以隐式转换为const T*，添加const限定符不破坏数据完整
const-> 非const：禁止，const T*不能隐式转换为T*

**引用与值的兼容性**
引用T&与T不能隐式互换，T&必须转换到已有对象；引用必须显式绑定

**对象与指针的兼容性**
对象T不能直接赋值给指针 ’ T*
指针需要内存地址，而对象是值类型，二者不兼容

- C++类型转换与未定义问题：
1. const_cast不对已经const的成员使用
2. reinterpret_cast注意双关行为
3. 向下转型使用动态dynamic_cast
4. 注意数组与指针的越界转换

- 多重继承中，若两个基类有同名函数show()派生类未重写，调用d.show()编译错误，二义性

### note
**继承中的函数查找规则：**
先查找派生类，如果找到：  参数匹配直接调用/**不匹配不会继续查找基类（Hide基类）**
                        如果未找到：向上查找基类，按普通继承规则处理


## 多态

- 虚函数本质是动态绑定（运行时多态），依赖对象的动态类型（通过虚函数表现）
- 静态成员函数属于类而非对象，没有this指针不能访问对象的虚函数表，不能是虚函数
- 虚函数可以在构造函数中被调用，可以在纯虚函数中声明，可以被override覆盖再派生类中
- 实现多态的方式：虚函数，重写override(覆盖)
- 动态类型决定实际调用哪个函数版本
- virtual void f() final; 表明“该函数为最终覆盖，禁止后续派生类再次覆盖”

- 抽象类定义或继承至少一个纯虚数且不能被实例化
- 不能定义其对象是抽象类的特性
- 纯虚函数是一个在基类中说明的虚函数，再该基类中没有定义但是要求所有派生类都定义自己的版本
- 若基类的析构函数是虚函数，则派生类的析构函数自动成为虚函数
- **C++接口interface**:纯虚函数，不能实例化，无成员变量，强制派生类实现特定方法

- 以下代码
```cpp
#include<iostream>
#include<typeinfo>
using namespace std;

class Base{virtual void func(){} }; //基类（含虚函数）
class Derived : public Base(){ }; //派生类

int main(){
    Base *ptr =new Derived(); //基类指针指向派生类对象
    cout<<typeid(ptr).name<<endl;
    cout<<typeid(*ptr).name<<endl;
    return 0;
}
```
输出为：
- typeid(ptr).name输出静态类型（编译时类型），实际类型为Base*指针
- typeid(*ptr).name输出动态类型（运行时类型），实际返回指向的对象类型
  
- dynamic_cast转换失败：若转换类型为指针，则返回nullptr;若为引用，抛出bad_cast异常
- final类：禁止继承
- final虚函数：禁止覆盖，非虚函数不能用final,继承final类直接导致编译错误

### note
- 静态类型：编译时确定，向下转型：派生类->基类
- 动态类型：运行时确定，向上转型：基类->派生类

**函数重载与函数覆盖核心区别**

|特性	|函数重载overload	|函数覆盖override|
|----|----|---|
|定义	|同一作用域内，函数名相同但是参数列表不同	|派生类重新定义基类虚函数，函数签名完全一致|
|目的	|提供统一功能的多种实现方式	|实现运行时多态，允许通过基类指针/引用调用派生类的特定实现|
|作用域	|同一作用域内	|跨继承（基类&派生类）|
|继承要求|	不依赖继承关系|	必须继承自包含虚函数的基类|
|多态类型|	编译时多态（静态绑定）	|运行时多态（动态绑定）|
|绑定时机|	编译时根据参数类型决定调用哪一个函数	|运行时根据对象的实际类型（动态类型）决定调用哪一个函数|
|签名	|参数列表必须不同，返回类型不限	|函数签名完全一致|
|关键字	|无|	基类函数virtual 派生类可override|


## 模板template

- 同时存在模板与非模板，调用优先匹配非模板
- 非类型模板形参的特点是：必须是编译时常量表达式；例如：stack< int,10 >中“10”是非类型模板形参
- 包含编译模式要求：模板定义在调用处可见
- 模板重载的优先级顺序：非模板调用> 模板特化> 通用模板
- 若同时存在普通函数与函数模板（若参数类型匹配）优先调用普通函数，若不匹配普通函数再考虑模板函数；函数模板成功匹配，就实例化该函数模板，并调用
- 数组引用作为模板参数的函数定义：template< typename T,int N > void print(T(&arr))[N];

### note
- 函数模板不参与隐式转换
  

## STL

- queue:
  - push操作将元素加入队尾（FIFO）
  - pop操作移除队列的队首元素
  - front访问队首元素

- 多数实现中，vector容量按指数增长（0-1-2-4-8），如当前容器容量为4，再插入一个元素，size=5,capacity=8；此时删除一个元素，size=4,capacity=8(不随删除而改变容量)
- resize操作不会改变原本vector容器的capacity,只改变size
  
- 预留容量：a.reserve(m)，预分配至少m个元素的内存
    后续再增加元素，内存分配的增加是根据m的倍数增加的
    例如：原capacity=16,reserve(50)，在50的基础上增加16，新的capacity为100（50*2）

- shrink_to_fit()：尝试减少capacity以匹配size(capacity = size)
- priority_queue 提供优先级队列实现，默认自动降序排列，队首元素始终为最大元素
    若想使用升序：priority_queue< int,vector< int >,greater< int >> minHeap;(队首始终最小元素)

## 异常处理

- 异常处理的“检测与处理分离”思想指：一个函数可以只负责检测并抛出异常，而具体处理任务交给其**调用链上层的函数**完成
- try-catch匹配：一个异常抛出，程序先执行第一个类型匹配的catch块，跳过其他catch块
- catch块中，使用 catch(const exception& e)相较于catch(exception e)捕获异常，优势在于按引用捕获可以避免额外的对象拷贝开销，防止“对象切片问题”
- 若一个声明为noexcpet的函数实际抛出异常，程序会立即terminate
- catch块中，若要将捕获到的异常原封不动重新抛出，使用throw,不带任何表达式的throw关键字
- 一个继承自exception的自定义异常也可以被excpetion的catch块处理

### note
```cpp
try{ 
    //可能抛出异常的代码
    throw SomeException(“Error message”);//抛出异常
}

catch(const someException& ex){ 
    //处理SomeException类型的异常
    cerr<<ex.what()<<endl;
}
catch(...){ 
    //捕获所有未被处理的异常{
    cerr<<"unknown exeption"<<endl;
}
```
- Summary:
    ·throw:抛出异常（内置类型、标准异常或自定义类）
    ·try-catch:捕获并处理异常（支持多级捕获）
    ·标准异常：优先使用exception派生类
    ·自定义异常：继承exception实现what()
    #include <stdexcept>
    Logic_error
    Runtime_error

- 异常传播

异常从抛出点（throw）到捕获点（catch）的传递过程，=
·基本原则：
1. 调用栈展开
当异常被抛出时：程序立即停止当前执行流；
        从当前函数开始，**沿调用栈向上查找**匹配的catch块
        如果找到匹配的catch，执行该块代码并继续程序
        如果无法匹配，调用terminate()终止程序

2. 局部对象析构
栈展开过程中**所有局部对象的析构函数**都会被调用

【异常传播路径】
1. 单层传播：异常在**同一个函数的try-catch块**中处理
2. 跨函数传播：异常通过**调用栈向上传播**，直到被捕获
3. 未被捕获的异常：如果异常未被任何catch捕获，终止程序terminate()

【多级catch匹配规则】
1. 按顺序匹配：catch块从上到下检查，第一个匹配的类型被执行
2. 基类捕获所有派生类异常：catch(const std::exception& )可以捕获任何标准异常
3. catch(...)捕获所有未被处理的异常（用于日志或清理）

【异常传播中的资源管理（RAII）】
问题：如果一场传播导致函数提前推出，确保资源被释放
解决方案：使用RAII封装资源

【阻止异常传播的机制】
1. noexcept函数：标记函数不抛出异常，用于优化性能或强制契约
2. 捕获并处理所有异常catch(...)