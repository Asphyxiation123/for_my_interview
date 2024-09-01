# 0 相关函数技巧
## Swap 函数
```C++
swap(a,b);
a,b可以为容量相同的任何元素，包括变量，数组，变量指针等，如果只是变量，需要加引用，如下;
swap(&a,&b);
```

## 字符串判断函数
```C++
isdigit(c)  //判断c字符是不是数字
isalpha(c)  //判断c字符是不是字母
isalnum(c)  //判断c字符是不是数字或者字母
tolower(c)  //转为小写
toupper(c)  //转为大写
```

## 字符串和数值之间转换
```C++
int num = 100;
string str = to_string(num); //整形转字符串
int number = stoi(str);  //字符串转为整形 stol()是字符串转为长整形
注：该函数不能处理将单个字符转化

atoi(str)
```
`stoi ()` **会**对转化后的数进行检查，判断是否会超出 int 范围，如果超出范围就会报错；  
`atoi ()` **不会**对转化后的数进行检查，超出上界，输出上界，超出下界，输出下界；
## 迭代器的二分 lower_bound
```C++
vector<int> nums{1,2,34,44,99};
int k = lower_bound(nums.begin(), nums.end(), 56) - nums.begin(); //第一个大于等于目标值的迭代器位置
int k = upper_bound(nums.begin(), nums.end(), 56) - nums.begin(); // 找到第一个大于目标值的迭代器位置
```

## 字符串转化为小写
```C++
transform(str.begin(), str.end(), str.begin(), ::tolower());
```

## 小根堆
```C++
priority_queue<int> pq; //默认是大根堆
priority_queue<int, vector<int>, greater<int>> pq; //小根堆
```

## 快速初始化数组
```C++
// 注意：这个函数是按字节初始化的
memset(nums, 0, sizeof nums);
memset(nums, -1, sizeof nums);
memset(nums, 0x3f, sizeof nums);
```

## C++11 特性
```C++
auto p = new ListNode(); // auto 关键字
Node* pre = nullptr   // nullptr代替NULL
unordered_map<int,int> mp; //哈希表 内部是无序的
unordered_set<int> st; //无序集合
```

## Bitset
```C++
uint32_t reverseBits(uint32_t n) {
    string s = bitset<32>(n).to_string();
    reverse(s.begin(), s.end());
    return bitset<32>(s).to_ulong();
}
```

## 字符串分割
```C++
string s = "hello world my name is yao jun";
stringstream ss(s);
string str;
int cnt = 0;
while(ss >> str){
    cnt++;
    cout<<str<<endl;
}
cout<<cnt<<endl;
```

```
执行结果：
hello
world
my
name
is
yao
jun
7
```

## 四舍五入保留小数
```C++
char str[10];
double num = 22.23434;
sprintf(str, "%.2f", num);
string s = str;
cout<<s<<endl;

执行结果：
22.23


直接使用round
float num= round(1.556*100)/100;
或者：
cout<<fixed<<setprecision(2)<<x<<endl;
```

## 字符串按格式拆分
```C++
string a = "12:59:36";
char str2[100];
memcpy(str2, a.c_str(), strlen(a.c_str()));
int u, v, w;
sscanf(str2, "%d:%d:%d", &u, &v, &w);
cout<<u<<" "<<v<<" "<<w<<endl;

执行结果：
12 59 36
```

## 相同字符的字符串
```C++
string s(10, 'a');
cout<<s<<endl;

执行结果:
aaaaaaaaaa
```

## 结构体排序
```C++
struct node{
    int a, b;
    // 从小到大排序
    bool operator < (const node& node_)const{
        if(a != node_.a) return a < node_.a;
        return b < node_.b;
    }
};
int main(){
    vector<node> tt;
    tt.push_back({1,5});
    tt.push_back({2,3});
    sort(tt.begin(), tt.end());
    for(auto &node: tt){
        cout<<node.a<<" "<<node.b<<endl;
    }
    return 0;
}

执行结果：
1 5
2 3
```


# 1. C++常用数据结构
## 迭代器通用操作

## 1.1 栈及其应用 stack
#stack
```C++
stack <int> stk;//创建栈

c++栈的方法的基本用法：
push(): 向栈内压入一个成员；
pop(): 从栈顶弹出一个成员；
empty(): 如果栈为空返回true，否则返回false；
top(): 返回栈顶元素，但不删除成员；
size(): 返回栈内元素的大小；
```
## 1.2 队列
队列是一种先进先出（First in First out，FIFO）的数据类型。<span style="background:#fff88f">每次元素的入队都只能添加到队列尾部，出队时从队列头部开始出。</span>
队列的头部便是最靠近后面的一个元素，尾则是新加入的元素。
```
------<--------------<--------------<-------------<---
|front|    |    |    |    |     |     |     |back
```

C++中，使用队列需要包含头文件< queue>。C++中队列的基本操作如下：
```C++
push () ：入队。在队列尾端添加一个元素，无返回值；
pop () ：出队。将队列头部元素删除（出队），无返回值；
front () ：获得队列头部元素。此函数返回值为队列的头部元素，常与 pop ()函数一起，先通过 front ()获得队列头部元素，然后将其从队列中删除；
size () ：获得队列大小。此函数返回队列的大小，返回值是“size_t”类型的数据，“size_t”是“unsigned int”的别名。
empty () ：判断队列是否为空。此函数返回队列是否为空，返回值是 bool 类型。队列空：返回 true；不空：返回 false。
back () ：返回队列尾部元素，就是队列中最后一个进去的元素。
```
### 优先队列（大根堆、小根堆）
大根堆是把大的元素放堆顶，小根堆就是把小的元素放到堆顶。优先队列 `priority_queue` 默认使用的是大根堆。
自定义数据类型即可实现堆的排序，例子：

```C++
#include <iostream>
#include<queue>
#include<cstdlib>
using namespace std;
struct Node{
    int x,y;
    Node(int a=0, int b=0):
        x(a), y(b) {}
};

struct cmp{
    bool operator()(Node a, Node b){
        if(a.x == b.x)  return a.y>b.y;
        return a.x>b.x;
    }
};

int main(){
    priority_queue<Node, vector<Node>, cmp>p;

    for(int i=0; i<10; ++i)
        p.push(Node(rand(), rand()));

    while(!p.empty()){
        cout<<p.top().x<<' '<<p.top().y<<endl;
        p.pop();
    }//while
    //getchar();
    return 0;


```
#### 构造函数
```C++
priority_queue<int> q; 　　　　　　　　　　  
//通过操作，按照元素从大到小的顺序出队
priority_queue<int,vector<int>, greater<int> > q;  　　
//通过操作，按照元素从小到大的顺序出队
priority_queue<int> q(gifts.begin(),gifts.end());
```
构造函数参数：待输入队列元素类型，底层构造数据结构（容器类型），比较函数
#### 自定义优先级
```C++
struct cmp {     
　　operator bool ()(int x, int y)     
　　{        
　　　　 return　x > y;　　 // x小的优先级高       
　　　　 //也可以写成其他方式，
　　　　 //如： return p[x] > p[y];表示p[i]小的优先级高
　　}
};

定义：
priority_queue<int, vector, cmp> q; //定义方法
//其中，第二个参数为容器类型。第三个参数为比较函数。
```


```C++
struct node {     
　　int x, y;     
　　friend bool operator < (node a, node b)     
　　{         
　　　　return a.x > b.x;    //结构体中，x小的优先级高     
　　}
};

定义：
priority_queue<node>q; //定义方法
//在该结构中，y为值, x为优先级。
//通过自定义operator<操作符来比较元素中的优先级。
//在重载”<”时，最好不要重载”>”，可能会发生编译错误
```

#### 使用总结

|     用法     |                作用                 |
| :--------: | :-------------------------------: |
|  q.top ()  |      返回 priority_queue 的首元素       |
| q.push ()  |   向 priority_queue 中加入元素到队尾 并排序   |
| q.size ()  |    返回 priority_queue 当前的长度（大小）    |
|  q.pop ()  |     从 priority_queue 队头删除一个元素     |
| q.empty () | 返回 priority_queue 是否为空，1 为空、0 不为空 |

### 优先队列自定义排序
#### 重载<, 在对象内
```C++
//功能：先比较第一个值，再比较第二个
struct node{
    int a, b;
    // 在优先队列中，跟排序的规则是反的，这里是指a大的排在前面，a相同时，b大的排在前面
    bool operator < (const node& node_)const{
        if(a != node_.a) return a < node_.a;
        return b < node_.b;
    }
};
int main(){
    priority_queue<node> pq;
    pq.push({1,5});
    pq.push({2,3});
    pq.push({2,5});
    while(!pq.empty()) {
        cout<<pq.top().a<<" "<<pq.top().b<<endl;
        pq.pop();
    }
    return 0;
}

执行结果
2 5
2 3
1 5
```
#### 自定义比较器结构体
```C++
struct Comp
{
	bool operator()(const Person& p1, const Person& p2) const
	{return p1.getage() > p2.getage()}
};
priority_queue<Person,vector<Person>,Comp>q
```
#### 自定义比较函数（麻烦）
```C++
bool cmp(const Person &p1, const Person& p2)
{
	return p1.getasge()<p2.getage();
}
priority_queue<Person,vector<Person>,bool(*)(const Person &p1, const Person& p2)>q(cmp)
```

#### Lamda 表达式
```C++
auto cmp3 = [](cosnt Person& p1, const Person& p2){
	return p1.age() < p2.age();
}

priority_queue<Person,vector<Person>,decltype(cmp3)> q(cmp3)
```
## 1.3 set & map
底层实现为平衡二叉树，故时间复杂度为logn
```C++
#include <set>
#include<unordered_set>
#include<map>
#include <unordered_map>
```
其中，加入unordered 前缀意味者不需要排序。如果没有unordered 前缀，那么该集合中的元素将会按照红黑树自动排序，这在我们需要有序数据或是遍历的时候较为有用。而加入unordered 前缀后，则集合中的元素会基于哈希表组织，插入和查找的时间复杂度很低（常数时间）。

以下是一些它们常用的函数（加入 unordered 前缀与不加对应的函数是几乎一致的，只是内部实现略有不同）。
### set 常用用法
```C++
// 集合
set<int> s;

// 常用函数
int n=s.size();
s.insert(num);
s.erase(num);
s.count(num); // 常用来判断有无这一元素

// 遍历元素
set<int>::iterator it;
for(it = s1.begin(); it!=s1.end(); it++){ //自动排序元素
    cout<<*it<<endl;  // 这里的it是个指向数据的指针
}
//c++ 11
for(auto it : s){
    cout<<it<<endl;   // 这里的it就是数据本身
}

```

#### Set 自动排序实例
```C++
#include <iostream>
#include <set>
using namespace std;

int main() {
    int arr[] = {5, 2, 8, 3, 1};
    set<int> s(arr, arr + 5);
    for (auto x : s) {
        cout << x << " ";
    }
    return 0;
}

out:
1 2 3 5 8
```

### map 常用用法
#### Map 定义
```c
// map
map<string, int> m;
//   key   value
```
#### 常用函数
包括 map 插入等
```c++
// 插入元素方法
int n=m.size();
m[key]=value; // 最简单的插入方法（个人认为）
m.insert(pair<string,int>("sd",19));
//擦书判断是否有某个元素
m.erase(key);
s.count(key); // 常用来判断有无这一元素
```
#### map 遍历模板
```c++
// 遍历元素
map<string, int>::iterator it;
for (it = m.begin(); it != m.end(); it++) {
    cout<<it->first<<" "<<it->second<<endl;
}
// c++ 11
for(auto it : m){
    cout<<it.first<<" "<<it.second<<endl;
}
```
#### 查找
Map 查找所查找的是键 (key)，有以下两个函数：
##### （1）find 函数
find函数查找成功会返回指向它的迭代器，没有找到的话，返回的是end这个迭代器
##### （2）count 函数
count函数的意思就是查找这个键的出现次数，map中键是唯一的，所以它的值要么是0要么是1，是1不就是查找成功吗，不过它的缺点也可以知道，它可以确定是否存在这个键，可是却不能确定这个键的位置



## 1.4 string 类型
### 1 .4.1 string 的构造函数
```C++
**无参构造：**
		string(); 
		创造了一个空的字符串   
		使用时 如 string a;
**有参构造**
	string(const char* s);
		使用一个string对象初始化另一个string对象
		char *s="hello world";
		string a(s);
	string(int n, char c);
		使用n个字符c初始化 
		string a(5,'a');
		输出的结果就是:aaaaa；
**拷贝构造**
	string(const string& str);
	使用一个string对象初始化另一个string对象
	如string a2(a)；
	将字符串a中的值复制（copy）过来；
```
### 1.4.2 string 容器的基本操作
#### 1.4.2.1 赋值操作
在 string 模板中提供了两个赋值的操作一种是靠操作符重载重置“ = ”去赋值；第二种是定义了 assign 函数去赋值；下面这是全部的赋值操作，实际上我们用的比较多的还是第一种方法 `operator=` 这种方式是比较实用的。
```C++
string s="人生苦短，我用c++";

string s1=s;
string s2='a‘;
string s3;
s3.assign("人生苦短，我用c++");
string s4;
s4.assign("人生苦短，我用c++",5);
string s5;
s5.assign(s4);
string s6;
s6.assign(s5,5);
```
其功能为：
```C++
要记住位置是从0开始的（和数组一样），后面都是一样
1.
string& operator=(const char* s);` //char*类型字符串 赋值给当前的字符串
2.
string& operator=(const string &s); //把字符串s赋给当前的字符串
3.
string& operator=(char c); //字符赋值给当前的字符串
4.
string& assign(const char *s); //把字符串s赋给当前的字符串
5.
string& assign(const char *s, int n); //把字符串s的前n个字符赋给当前的字符串
6.
string& assign(const string &s); //把字符串s赋给当前字符串
7.
string& assign(int n, char c); //用n个字符c赋给当前字符串
```
#### 1.4.2.2 字符串拼接操作
一个是重载了 +=另外一个是 append 函数 （这点的操作与 python 中比较类似）
```C++
//字符串拼接
//第一种用法
string str1="我";
str1+="在学c++";
str1+='!';
string str2="发现太难了，我不学了。";
str1+=str2;
//实际输出的就是   我在学c++!发现太难了，我不学了。

//第二种用法
string str3="12345";
char *s="678910"
string str4="678";
str3.append(str4);
str3.append(s);
str3.append(s,2); //前两个字符，也就是 67
str3.append(str4,2,3); 从第二个开始到结束的3个字符

函数原型为：
string& operator+=(const char* str); //重载+=操作符

string& operator+=(const char c); //重载+=操作符

string& operator+=(const string& str); //重载+=操作符

string& append(const char *s); //把字符串s连接到当前字符串结尾

string& append(const char *s, int n); //把字符串s的前n个字符连接到当前字符串结尾

string& append(const string &s); //同operator+=(const string& str)

string& append(const string &s, int pos, int n);//字符串s中从pos开始的n个字符连接到字符串结尾
```
### 1.4.3 字符串查找和替换
#### 查找 find：
```C++
1. size_t find (constchar* s, size_t pos = 0) const;

  //在当前字符串的pos索引位置开始，查找子串s，返回找到的位置索引，

    -1表示查找不到子串

2. size_t find (charc, size_t pos = 0) const;

  //在当前字符串的pos索引位置开始，查找字符c，返回找到的位置索引，

    -1表示查找不到字符

3. size_t rfind (constchar* s, size_t pos = npos) const;

  //在当前字符串的pos索引位置开始，反向查找子串s，返回找到的位置索引，

    -1表示查找不到子串

4. size_t rfind (charc, size_t pos = npos) const;

  //在当前字符串的pos索引位置开始，反向查找字符c，返回找到的位置索引，-1表示查找不到字符

5. size_tfind_first_of (const char* s, size_t pos = 0) const;

  //在当前字符串的pos索引位置开始，查找子串s的字符，返回找到的位置索引，-1表示查找不到字符

6. size_tfind_first_not_of (const char* s, size_t pos = 0) const;

  //在当前字符串的pos索引位置开始，查找第一个不位于子串s的字符，返回找到的位置索引，-1表示查找不到字符

7. size_t find_last_of(const char* s, size_t pos = npos) const;

  //在当前字符串的pos索引位置开始，查找最后一个位于子串s的字符，返回找到的位置索引，-1表示查找不到字符

8. size_tfind_last_not_of (const char* s, size_t pos = npos) const;

 //在当前字符串的pos索引位置开始，查找最后一个不位于子串s的字符，返回找到的位置索引，-1表示查找不到子串
```
#### 替换replace
`replace` 函数:
`replace` 在替换时，要指定从哪个位置起，多少个字符，替换成什么样的字符串。替换的字符串，与原字符串的长度没有关系
```C++
String s="Ilove";
int pos1=s.find ("v", 0);//记得用一个 int 变量去接收结果
//pos 1 为 3
int pos2=s.rfind ("v",s.size ());
//pos 2 为 1

string s2=s.replace (0,1,"You");
//把 I 替换成 You


函数原型：

Int find (const string& str, int pos = 0) const; //查找 str 第一次出现位置, 从 pos 开始查找

Int find (const char* s, int pos = 0) const; //查找 s 第一次出现位置, 从 pos 开始查找

Int find (const char* s, int pos, int n) const; //从 pos 位置查找 s 的前 n 个字符第一次位置

Int find (const char c, int pos = 0) const; //查找字符 c 第一次出现位置

Int rfind (const string& str, int pos = npos) const; //查找 str 最后一次位置, 从 pos 开始查找

Int rfind (const char* s, int pos = npos) const; //查找 s 最后一次出现位置, 从 pos 开始查找

Int rfind (const char* s, int pos, int n) const; //从 pos 查找 s 的前 n 个字符最后一次位置

Int rfind (const char c, int pos = 0) const; //查找字符 c 最后一次出现位置

String& replace (int pos, int n, const string& str); //替换从 pos 开始 n 个字符为字符串 str

String& replace (int pos, int n, const char* s); //替换从 pos 开始的 n 个字符为字符串 s
```

#### 字符串比较
功能描述：
字符串之间的比较
比较方式：字符串比较是按字符的 ASCII 码进行对比
```C
相等返回 0 
>返回 1
<返回-1


函数原型：

Int compare (const string &s) const; //与字符串 s 比较

Int compare (const char *s) const; //与字符串 s 比较
```

#### 存取
String 中单个字符存取方式有两种
```C++
Char& operator[](int n); //通过[]方式取字符

Char& at (int n); //通过 at 方法获取字符
```
如：
```C++
String a="hello world";
Cout<<a[0]<<endl;
cout<<a.at[0]<<endl;
//两个结果都是 h
```

#### string插入和删除
功能描述：
对 string 字符串进行插入和删除字符操作
```C++
函数原型：

String& insert (int pos, const char* s); //插入字符串

String& insert (int pos, const string& str); //插入字符串

String& insert (int pos, int n, char c); //在指定位置插入 n 个字符 c

String& erase (int pos, int n = npos); //删除从 Pos 开始的 n 个字符

String s="234"；
s.insert (0,"01");
//此时 s 为 01234
String s1 = "567";
int n=s.size ();
s.insert (n, s1);
//此时 s 为 01234567
s.insert (s.size (), 3,'1');
//此时 s 为 01234567111
s.erase (0,2);
//此时 s 为 234567111
```

#### 子串
功能描述：从字符串中获取想要的子串
```C++
函数原型：

String substr (int pos = 0, int n = npos) const; //返回由 pos 开始的 n 个字符组成的字符串

String s='abcde';
string sub=s.substr (0,2);
//sub 为 abc;

```
这个功能非常好用，可以用来解决要去查找目标子串的问题，与前面的 find 函数配合使用。
#### 1.4.4 其他方法
### 数据类型转化
#### int- string
```C++

s=to_string(num)

2. string转为各类无符号整数
stoul(str);

3.string转为int
stoi();
```
### 判断数字字母
```C
islower(char c) 是否为小写字母
isupper(char c) 是否为大写字母
isdigit(char c) 是否为数字
isalpha(char c) 是否为字母
isalnum(char c) 是否为字母或者数字
toupper(char c) 字母小转大
tolower(char c) 字母大转小
```

___
## 2 vector 类型
### 2.1 vector 的初始化
可以有五种方式,举例说明如下：
```C++
(1) vector<int> a(10); //定义了10个整型元素的向量（尖括号中为元素类型名，它可以是任何合法的数据类型），但没有给出初值，其值是不确定的。
（2）vector<int> a(10,1); //定义了10个整型元素的向量,且给出每个元素的初值为1
（3）vector<int> a(b); //用b向量来创建a向量，整体复制性赋值
（4）vector<int> a(b.begin(),b.begin+3); //定义了 a 值为 b 中第0个到第2个（共3个）元素。该构造函数为左闭右开区间。
（5）int b[7]={1,2,3,4,5,9,8};
        vector<int> a(b,b+7); //从数组中获得初值
(6) 直接赋值 vector <int> v;v={1,2,3,4,5};
```
### 2.2 vector 的几个重要操作

```C++
(1)a.assign(b.begin(), b.begin()+3); //b为向量，将b的0~2个元素构成的向量赋给a
 (2) a.assign(4,2); //是a只含4个元素，且每个元素为2
（3）a.back(); //返回a的最后一个元素
（4）a.front(); //返回a的第一个元素
（5）a[i]; //返回a的第i个元素，当且仅当a[i]存在2013-12-07
（6）a.clear(); //清空a中的元素
（7）a.empty(); //判断a是否为空，空则返回ture,不空则返回false
（8）a.pop_back(); //删除a向量的最后一个元素
（9）a.erase(a.begin()+1,a.begin()+3); //删除a中第1个（从第0个算起）到第2个元素，也就是说删除的元素从a.begin()+1算起（包括它）一直到a.begin()+         3（不包括它）
（10）a.push_back(5); //在a的最后一个向量后插入一个元素，其值为5
（11）a.insert(a.begin()+1,5); //在a的第1个元素（从第0个算起）的位置插入数值5，如a为1,2,3,4，插入元素后为1,5,2,3,4
（12）a.insert(a.begin()+1,3,5); //在a的第1个元素（从第0个算起）的位置插入3个数，其值都为5
（13）a.insert(a.begin()+1,b+3,b+6); //b为数组，在a的第1个元素（从第0个算起）的位置插入b的第3个元素到第5个元素（不包括b+6），如b为1,2,3,4,5,9,8         ，插入元素后为1,4,5,9,2,3,4,5,9,8
a.insert(a.end(), b.begin(), b.end());//拼接vector a+b
（14）a.size(); //返回a中元素的个数；
（15）a.capacity(); //返回a在内存中总共可以容纳的元素个数
（16）a.resize(10); //将a的现有元素个数调至10个，多则删，少则补，其值随机
（17）a.resize(10,2); //将a的现有元素个数调至10个，多则删，少则补，其值为2
（18）a.reserve(100); //将a的容量（capacity）扩充至100，也就是说现在测试a.capacity();的时候返回值是100.这种操作只有在需要给a添加大量数据的时候才显得有意义，因为这将避免内存多次容量扩充操作（当a的容量不足时电脑会自动扩容，当然这必然降低性能） 
（19）a.swap(b); //b为向量，将a中的元素和b中的元素进行整体性交换
（20）a==b; //b为向量，向量的比较操作还有!=,>=,<=,>,<
```
### 2.3 顺序访问 vector 的几种方式
举例说明如下：
#### （1）向向量 a 中添加元素
1、直接添加
```C++
vector<int> a;
for(int i=0;i<10;i++){
	a.push_back(i);
}
```

2、也可以从数组中选择元素向向量中添加
```C++
int a[6]={1,2,3,4,5,6};
vector<int> b；
for(int i=1;i<=4;i++)
	b.push_back(a[i]);
```

3、也可以从现有向量中选择元素向向量中添加
```C++
int a[6]={1,2,3,4,5,6};
vector<int> b;
vector<int> c(a,a+4);
for(vector<int>::iterator it=c.begin();it<c.end();it++)
	b.push_back(*it);
 ```

4、也可以从文件中读取元素向向量中添加
```C++
ifstream in("data.txt");
vector<int> a;
for(int i; in>>i)
    a.push_back(i);
```

5、【误区】: vector 未初始化无法索引
```C++
vector<int> a;
for(int i=0;i<10;i++)
    a[i]=i;
//这种做法以及类似的做法都是错误的。刚开始我也犯过这种错误，后来发现，下标只能用于获取已存在的元素，而现在的a[i]还是空的对象
```
 
#### （2）从向量中读取元素
					  
1、通过下标方式读取
```C++
int a[6]={1,2,3,4,5,6};
vector<int> b(a,a+4);
for(int i=0;i<=b.size()-1;i++)
    cout<<b[i]<<" ";
 ```

2、通过遍历器方式读取
```C++
int a[6]={1,2,3,4,5,6};
vector<int> b(a,a+4);
for(vector<int>::iterator it=b.begin();it!=b.end();it++)
    cout<<*it<<" ";
 ```

## 3几种容器的重要的算法
### 3.1 lamda 表达式
#### lambda 表达式语法语法定义
```C++
[captures]( params ) specifiers exception ->reti{ body} 
```
#### lambda 表达式各个成员的解释
![[Pasted image 20240227101859.png]]

**_captures_ 捕获列表，lambda可以把上下文变量以值或引用的方式捕获，在body中直接使用。

**_tparams_ 模板参数列表（optional）**(c++20引入)，让lambda可以像模板函数一样被调用。

**_params_ 参数列表**，有一点需要注意，在c++14之后允许使用auto左右参数类型。

**_lambda-specifiers_ lambda说明符**_，_ 一些可选的参数，这里不多介绍了，有兴趣的读者可以去官方文档上看。这里比较常用的参数就是mutable和exception。其中，表达式(1)中没有trailing-return-type，是因为包含在这一项里面的。

**_trailing-return-type_ 返回值类型**，一般可以省略掉，由编译器来推导。

**_body_ 函数体**，函数的具体逻辑。

#### 捕获列表

上面介绍完了lambda表达式的各个成分，其实很多部分和正常的函数没什么区别，其中最大的一个不同点就是捕获列表。我在刚开始用lambda表达式的时候，还一直以为这个没啥用，只是用一个 [] 来标志着这是一个lambda表达式。后来了解了才知道，原来这个捕获列表如此强大，甚至我觉得捕获列表就是lambda表达式的灵魂。下面先介绍几种常用的捕获方式。

**[]** 什么也不捕获，无法lambda函数体使用任何
**[=]** 按值的方式捕获所有变量
**[&]** 按引用的方式捕获所有变量
**[=, &a]** 除了变量a之外，按值的方式捕获所有局部变量，变量a使用引用的方式来捕获。这里可以按引用捕获多个，例如 [=, &a, &b,&c]。这里注意，如果前面加了=，后面加的具体的参数必须以引用的方式来捕获，否则会报错。
**[&, a]** 除了变量a之外，按引用的方式捕获所有局部变量，变量a使用值的方式来捕获。这里后面的参数也可以多个，例如 [&, a, b, c]。这里注意，如果前面加了&，后面加的具体的参数必须以值的方式来捕获。
**[a, &b]** 以值的方式捕获a，引用的方式捕获b，也可以捕获多个。
**[this]** 在成员函数中，也可以直接捕获this指针，其实在成员函数中，[=]和[&]也会捕获this指针。

  
```C++
#include <iostream>

int main()
{
    int a = 3;
    int b = 5;
    
    // 按值来捕获
    auto func1 = [a] { std::cout << a << std::endl; };
    func1();

    // 按值来捕获
    auto func2 = [=] { std::cout << a << " " << b << std::endl; };
    func2();

    // 按引用来捕获
    auto func3 = [&a] { std::cout << a << std::endl; };
    func3();

    // 按引用来捕获
    auto func4 = [&] { std::cout << a << " " << b << std::endl; };
    func4();
}
```

示例
```C++
int x=5:
auto foo= [x](int y)->int { return x *y};
std: cout < foo (8)<< std: endl;
```

### 3.2 容器算法
使用时需要包含头文件：
``#include<algorithm>``
```C++
（1）sort(a.begin(),a.end()); 
//对a中的从a.begin()（包括它）到a.end()（不包括它）的元素进行从小到大排列

（2）reverse(a.begin(),a.end()); 
//对a中的从a.begin()（包括它）到a.end()（不包括它）的元素倒置，但不排列，如a中元素为1,3,2,4,倒置后为4,2,3,1


（3）copy(a.begin(),a.end(),b.begin()+1); 
//把a中的从a.begin()（包括它）到a.end()（不包括它）的元素复制到b中，从b.begin()+1的位置（包括它）开始复制，覆盖掉原有元素

（4）find(a.begin(),a.end(),10); 
//在a中的从a.begin()（包括它）到a.end()（不包括它）的元素中查找10，若存在返回其在向量中的位置
```
注：此时数据的指向在数据前；

### Lamda 表达式与算法函数
```C++
struct Item
{
    Item(int aa, int bb) : a(aa), b(bb) {} 
    int a;
    int b;
};
	
int main()
{
    std::vector<Item> vec;
    vec.push_back(Item(1, 19));
    vec.push_back(Item(10, 3));
    vec.push_back(Item(3, 7));
    vec.push_back(Item(8, 12));
    vec.push_back(Item(2, 1));

    // 根据Item中成员a升序排序
    std::sort(vec.begin(), vec.end(),
        [] (const Item& v1, const Item& v2) { return v1.a < v2.a; });

    // 打印vec中的item成员
    std::for_each(vec.begin(), vec.end(),
        [] (const Item& item) { std::cout << item.a << " " << item.b << std::endl; });
	return 0;
}
```


#  3 Stringstream
可以用于分割被空格、制表符等符号分割的字符串
```C++
//Example：可以用于分割被空格、制表符等符号分割的字符串
#include<iostream>  
#include<sstream>        //istringstream 必须包含这个头文件
#include<string>  
using namespace std;  
int main(){  
    string str="i am a boy";  
    istringstream is(str);  
    string s;  
    while(is>>s)  {  
        cout<<s<<endl;  
    }  
} 
```

```C++
out:
i
am
a
boy
```
### 使用特定字符分割字符串
```C++
#include <iostream>
#include <sstream>
#include <vector>
 
using namespace std;
 
int main() {
        std::string data = "1_2_3_4_5_6";
        std::stringstream ss(data);
        std::string item;
        queue<string> q;
        cout << data << endl;
        while (std::getline(ss, item, '_')) 
            cout << item << ' ';  
}
//下面是例2
int main2() {
    std::string input = "apple,banana,orange,pear";
    std::stringstream ss(input);
    string token;
    while (std::getline(ss, token, ',')) {
        cout<<token<<endl;
    }
    // tokens 现在包含 ["apple", "banana", "orange", "pear"]
}
```
## 其他数据类型
### pair
```C++
pair<int,int> s ={1,2};
```

# getline ()

```C++
#include<bits/stdc++.h>
using namespace std;
int main(){
    string str;
    //输入aa,bb,cc
    getline(cin,str);
    cout<<str<<endl;//aa,bb,cc
    getline(cin, str, ',');
    cout<<str<<endl;//aa
    return 0;
}
```
## **与 `cin >>` 结合使用**

若需混合使用`cin >>`读取数值和`getline`读取字符串，为了避免`cin`遗留的换行符影响`getline`，可以在两者之间插入一个额外的`getchar()`来清空缓冲区
```C++
int num;
std::cin >> num;  // 读取一个整数
std::cin.ignore(); // 忽略剩余的当前行（可能包括空格和换行符）
std::getline(std::cin, input);  // 正常读取下一行字符串
```
# 位运算
## 一 . 位运算符：
- 逻辑运算符 ：
1. &：位‘与’；
2. ^：位‘异或’
3. ｜：位‘或’
4. ～：位‘非’，取反
- 移位运算符：
1. <<: 左移
2. >>: 右移

逻辑运算符都是（bit）为单位。

## 二 .使用方式
### （1）‘～’非，取反
位取反的操作符为“~”，0 变成 1，1 变成 0，需要注意的是，位取反运算并不会改变操作数的值，它只是再内存中进行的。如果想要改变需要重新赋值。
```C
#include <stdio.h>
Int main (void) {
	Unsigned char a = 15; //0000 1111
 	Unsigned char a1 = ~a; //1111 0000
 	Unsigned char a2 = ~a1;//0000 1111
 	Printf ("%d,%d,%d", a, a 1, a 2);
}
//输出为 15，240，15
```
### （2）位与运算&：
位与运算符的操作符为&，1 & 1 = 1，1 & 0 = 0， 0 & 1 = 0，0 & 0 = 0
```C
#include <stdio.h>
Int main (void) {
	Unsigned char a 1 = 3;  // 0000 0011
 	Unsigned char a 2 = 240; // 1111 0000
 	Unsigned char a 3 = 255; //1111 1111
	Printf ("%d,%d", a 1 & a 2, a 1 & a 3);
	Return 0;
}
//输出为 0，3
```

### （3）位或｜运算
位或运算的操作符为｜，将对两个操作数的每一位进行或运算，位“或”运算的准则如下：
1 ｜ 1 = 1， 1 ｜ 0 = 1， 0 ｜ 1 = 1， 0 ｜ 0 = 0
```C
#include <stdio.h>
Int main (void) {
	Unsigned char a = 169;  //1010 1001
 	Unsigned char a 1 = 240; //1111 0000
	Printf ("%d", a | a 1);
	Return 0;
}
//输出为 249
```
### （4）位异或^
位异或运算的操作符为^，将对两个操作数的每一位进行异或运算。运算准则为：
1 ^ 1 = 0, 1 ^ 0 = 0, 0 ^ 1 = 0, 0 ^ 0 = 0 (==相同为 0，不同为 1==)
```C
#include <stdio.h>
int main (void) {
	unsigned char a 1 = 169;  //1010 1001
 	unsigned char a 2 = 15; //0000 1111
	printf ("%d", a 1 ^ a 2);
	return 0;
}
//输出为 166
```
### （5）左移‘<<’和右移’>>’
```C
#include <stdio.h>
int main (void) {
	unsigned char a = 1; // 0000 0001
	printf ("%d,", a << 1); // 0000 0010	
	printf ("%d,", a << 3); // 0000 1000
	printf ("%d", a); //0000 0001 左移运算符不改变原来的值
	return 0;
}
//输出结果：2,8,1
```
###  例题 ：
#### 一、题目：力扣 191. 位 1 的个数
编写一个函数，输入是一个无符号整数（以二进制串的形式），返回其二进制表达式中数字位数为‘1’的个数（也被称为汉明重量）。

提示：

请注意，在某些语言（如 Java）中，没有无符号整数类型。在这种情况下，输入和输出都将被指定为有符号整数类型，并且不应影响您的实现，因为无论整数是有符号的还是无符号的，其内部的二进制表示形式都是相同的。
在 Java 中，编译器使用二进制补码记法来表示有符号整数。因此，在上面的示例 3 中，输入表示有符号整数 -3。

示例 1：

输入：00000000000000000000000000001011
输出：3
解释：输入的二进制串 00000000000000000000000000001011 中，共有三位为‘1’。
示例 2：

输入：00000000000000000000000010000000
输出：1
解释：输入的二进制串 00000000000000000000000010000000 中，共有一位为‘1’。
示例 3：

输入：11111111111111111111111111111101
输出：31
解释：输入的二进制串 11111111111111111111111111111101 中，共有 31 位为‘1’。

提示：

输入必须是长度为 32 的二进制串。

进阶：

如果多次调用这个函数，你将如何优化你的算法？

思考：用无符号数 1 结合 for 循环，利用按位与运算符和左移运算符遍历 n 二进制码的每一位，用 cnt 记录 n 二进制码中 1 的个数，出循环后返回 cnt 的值即可。

代码：
```C
int hammingWeight (uint 32_t n) {
    Int cnt = 0, i;
    Unsigned int a = 1;
    For (i = 0; i < 32; i++) {
        if (n & (a << i)) { //按位与运算符&只有 1 & 1 时才等于 1，其余情况都为 0，用左移运算符<<每次循环都让 a 的二进制码左移 i 位，最多左移 31 位就可以判断 n 的二进制码中的每一位是否为1
            ++cnt; //如果 n 这个位上的二进制码为 1 就会进循环，在这个地方++cnt
        }
    }
    return cnt;
}
```


## 二进制常见操作
| 功能 | 示例 | 位运算 |
| ---- | ---- | ---- |
| 去掉最后一位 | (101101->10110) | x >> 1 |
| 在最后加一个 0 | (101101->1011010) | x < < 1 |
| 在最后加一个 1 | (101101->1011011) | x < < 1+1 |
| 把最后一位变成 1 | (101100->101101) | x\|1 |
| 把最后一位变成 0 | (101101->101100) | x\|1-1 |
| 最后一位取反 | (101101->101100) | x ^ 1 |
| 把右数第 k 位变成 1 | (101001->101101, k=3) | x \| (1 < < (k-1)) |
| 把右数第 k 位变成 0 | (101101->101001, k=3) | x & ~ (1 < < (k-1)) |
| 右数第 k 位取反 | (101001->101101, k=3) | x ^ (1 < < (k-1)) |
| 取末三位 | (1101101->101) | x & 7 |
| 取末 k 位 | (1101101->1101, k=5) | x & ((1 < < k)-1) |
| 取右数第 k 位 | (1101101->1, k=4) | x >> (k-1) & 1 |
| 把末 k 位变成 1 | (101001->101111, k=4) | x \|(1 < < k-1) |
| 末 k 位取反 | (101001->100110, k=4) | x ^ (1 < < k-1) |
| 把右边连续的 1 变成 0 | (100101111->100100000) | x & (x+1) |
| 把右起第一个 0 变成 1 | (100101111->100111111) | x \| (x+1) |
| 把右边连续的 0 变成 1 | (11011000->11011111) | x \| (x-1) |
| 取右边连续的 1 | (100101111->1111) | (x ^ (x+1)) >> 1 |
| 去掉右起第一个 1 的左边 | (100101000->1000) | $x & (x ^ (x-1)) |
| 判断奇数 |  | (x&1)==1 |
| 判断偶数 |  | (x&1)==0 |
| 取左右边的 1 | x^(x - 1) |  |
| 消去最右边的 1 | x&(x - 1)   25 (x - 2) ^ (x - 1) ^ x = x + 1 |  |

