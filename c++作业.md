### 9.1

(a)list，因为可能需要在容器的中间位置插入元素

(b)deque，因为需要在头部进行元素的删除，deque效率更高

(c)vector，无具体的删除插入操作，未知数量，vector是个不错的选择。

### 9.20
```
#include<iostream>
#include<fstream>
#include<sstream>
#include<string>
#include<vector>
#include<list>
#include<deque>
using namespace std;
 
int main(int argc, char**argv)
{
	list<int> list1(5,7);
	deque<int> deque1;
	deque<int> deque2;
	list<int>::iterator it1 = list1.begin();
	for (it1; it1 != list1.end(); it1++)
	{
		if ((*it1)%2 == 0)
		{
			deque1.push_back(*it1);
		} 
		else
		{
			deque2.push_back(*it1);
		}
	}
 
	deque<int>::iterator it2 = deque1.begin();
	deque<int>::iterator it3 = deque2.begin();
	cout<<"偶数为：";
	for (it2; it2 != deque1.end(); it2++)
	{
		cout<<*it2<<" ";
	}
	cout<<endl;
	cout<<"奇数为：";
	for (it3; it3 != deque2.end(); it3++)
	{
		cout<<*it3<<" ";
	}
	
	return 0;
}
```
### 9.29

resize(100)会将其大小改为100个元素的大小，添加新元素并初始化，之后使用resize(10)会将之后的90个元素舍弃。

### 9.43
```
#include<iostream>
#include<fstream>
#include<sstream>
#include<string>
#include<vector>
#include<forward_list>
using namespace std;
 
void func(string &s, string &oldVal, string &newVal)
{
	int _size = oldVal.size();
	string::iterator it1 = s.begin();
	string::iterator it2 = newVal.begin();
	string::iterator it3 = newVal.end();
 
	 for (it1; it1 <= (s.end()-oldVal.size()+1); ++it1)
	{
		//pos可以由两个迭代器相减得到,返回截取到的string
		if((s.substr(it1-s.begin(),_size) == oldVal))//substr()的作用就是截取string中的一段
		{
			it1 = s.erase(it1,it1+_size);//返回的是最后一个被删除的元素之后的位置
			it1 = s.insert(it1, it2, it3);//VS2010运行出错，但是未找到原因何在，本质上说应该是没有错误的
			advance(it1,_size);//向前一定大小
		}
	}
}
 
int main(int argc, char**argv)
{
	string s = "abcdefg";
	string oldval = "bc";
	string newval = "asas";
	func(s,oldval,newval);
	cout<<s<<endl;
	return 0;
}
```
### 9.52
```
#include <iostream>
#include <vector>
#include <stack>
#include <string>
#include <cstdlib>
#include <cctype>
#include <cstring>
using namespace std;


vector<string> preParse(char *str)   //对中缀表达式进行预处理，分离出每个token
{
    vector<string> tokens;
    int len = strlen(str);
    char *p = (char *)malloc((len+1)*sizeof(char));  //注意不要用 char *p = (char *)malloc(sizeof(str))来申请空间
    int i=0,j=0;
    while(i<len)          //去除表达式中的空格
    {
        if(str[i]==' ')
        {
            i++;
            continue;
        }
        p[j++] = str[i++];
    }
    p[j]='\0';
    j=0;
    len = strlen(p);
    while(j<len)
    {
        char temp[2];
        string token;
        switch(p[j])
        {
            case '+':
            case '*':
            case '/':
            case '(':
            case ')':
                {
                    temp[0] =p[j];
                    temp[1] = '\0';
                    token=temp;
                    tokens.push_back(token);
                    break;
                }
            case '-':
                {
                    if(p[j-1]==')'||isdigit(p[j-1]))  //作为减号使用
                    {
                        temp[0] =p[j];
                        temp[1] = '\0';
                        token=temp;
                        tokens.push_back(token);
                    }
                    else    //作为负号使用
                    {
                        temp[0] ='#';
                        temp[1] = '\0';
                        token=temp;
                        tokens.push_back(token);
                    }  
                    break;
                }
            default:     //是数字
                {
                    i = j;
                    while(isdigit(p[i])&&i<len)
                    {
                        i++;
                    }
                    char *opd = (char *)malloc(i-j+1);
                    strncpy(opd,p+j,i-j);
                    opd[i-j]='\0';
                    token=opd;
                    tokens.push_back(token);
                    j=i-1;
                    free(opd);
                    break;
                }  
        }
        j++;
    }
    free(p);
    return tokens;
}



int getPriority(string opt)
{
    int priority;
    if(opt=="#")
        priority = 3;  
    else if(opt=="*"||opt=="/")
        priority = 2;
    else if(opt=="+"||opt=="-")
        priority = 1;
    else if(opt=="(")
        priority = 0;
    return priority;
}

void calculate(stack<int> &opdStack,string opt)
{
    if(opt=="#")  //进行负号运算
    {
        int opd = opdStack.top();
        int result = 0-opd;
        opdStack.pop();
        opdStack.push(result);
        cout<<"操作符:"<<opt<<" "<<"操作数:"<<opd<<endl; 
    }
    else if(opt=="+")
    {
        int rOpd = opdStack.top();
        opdStack.pop();
        int lOpd = opdStack.top();
        opdStack.pop();
        int result = lOpd + rOpd;
        opdStack.push(result); 

        cout<<"操作符:"<<opt<<" "<<"操作数:"<<lOpd<<" "<<rOpd<<endl; 
    }
    else if(opt=="-")
    {
        int rOpd = opdStack.top();
        opdStack.pop();
        int lOpd = opdStack.top();
        opdStack.pop();
        int result = lOpd - rOpd;
        opdStack.push(result); 
        cout<<"操作符:"<<opt<<" "<<"操作数:"<<lOpd<<" "<<rOpd<<endl; 
    }
    else if(opt=="*")
    {
        int rOpd = opdStack.top();
        opdStack.pop();
        int lOpd = opdStack.top();
        opdStack.pop();
        int result = lOpd * rOpd;
        opdStack.push(result); 
        cout<<"操作符:"<<opt<<" "<<"操作数:"<<lOpd<<" "<<rOpd<<endl; 
    }
    else if(opt=="/")
    {
        int rOpd = opdStack.top();
        opdStack.pop();
        int lOpd = opdStack.top();
        opdStack.pop();
        int result = lOpd / rOpd;
        opdStack.push(result); 
        cout<<"操作符:"<<opt<<" "<<"操作数:"<<lOpd<<" "<<rOpd<<endl; 
    }
}

int evaMidExpression(char *str)   //中缀表达式直接求值
{
    vector<string> tokens = preParse(str);
    int i=0;
    int size = tokens.size();

    stack<int> opdStack;     //存储操作数
    stack<string> optStack;   //存储操作符
    for(i=0;i<size;i++)
    {
        string token = tokens[i];
        if(token=="#"||token=="+"||token=="-"||token=="*"||token=="/")
        {
            if(optStack.size()==0)   //如果操作符栈为空
            {
                optStack.push(token);
            }
            else
            {
                int tokenPriority = getPriority(token);
                string topOpt = optStack.top();
                int topOptPriority = getPriority(topOpt);
                if(tokenPriority>topOptPriority)
                {
                    optStack.push(token);
                }
                else
                {
                    while(tokenPriority<=topOptPriority)
                    {
                        optStack.pop();
                        calculate(opdStack,topOpt);
                        if(optStack.size()>0)
                        {
                            topOpt = optStack.top();
                            topOptPriority = getPriority(topOpt);
                        }
                        else
                            break;

                    }
                    optStack.push(token);
                }
            }  
        }
        else if(token=="(")
        {
            optStack.push(token);
        }
        else if(token==")")
        {
            while(optStack.top()!="(")
            {
                string topOpt = optStack.top();
                calculate(opdStack,topOpt);
                optStack.pop();
            }
            optStack.pop();
        }
        else   //如果是操作数，直接入操作数栈
        {
            opdStack.push(atoi(token.c_str()));
        }
    }
    while(optStack.size()!=0)
    {
        string topOpt = optStack.top();
        calculate(opdStack,topOpt);
        optStack.pop();
    }
    return opdStack.top();
}


int main(int argc, char *argv[])
{
    char str[] = "((3+5*2)+3)/5+(-6)/4*2+3";
     vector<string> tokens = preParse(str);
     for(auto i = 0; i != tokens.size(); ++i)
     cout << tokens[i] << " ";
     cout << endl;
    cout<<evaMidExpression(str)<<endl;
    return 0;
}
```
### 10.3
```
#include<iostream>
#include<string>
#include<vector>
#include<algorithm>
#include<numeric>
using namespace std;
 
int main(int argc, char**argv)
{
	int a[10] = {0,1,2,5,4,5,4,5,4,5};
	vector<int> vec(a,a+10);
	cout<<"元素之和为："<<accumulate(vec.begin(),vec.end(),0);
 
	return 0;
}

```
### 10.15

[a](int &b){cout<<a+b;}

### 10.34

```
#include<vector>  
#include<algorithm>  
#include<numeric>  
#include<functional>
#include<iterator>
using namespace std;
using namespace placeholders;//占位符的命名空间
 
int main(int argc, char**argv)  
{  
	int a[5] = {1,2,3,4,5};
	vector<int> day1(a,a+5);
 
	for (auto it1 = day1.rbegin(); it1 != day1.rend() ; ++it1)//这里的++实际上是递减的
	{
		cout<<*it1<<" ";
	}
 
	return 0;  
} 
```
### 10.42

```
#include<iostream>  
#include<fstream>
#include<string>  
#include<vector> 
#include<list>
#include<algorithm>  
#include<numeric>  
#include<functional>
#include<iterator>
using namespace std;
using namespace placeholders;//占位符的命名空间
 
int main(int argc, char**argv)  
{ 
	string a[10] = {"sdc","sddc","sdec","sfdc","sdec","sdc","sdc","fsdc","sadc","fsdc"};
	list<string> list1(a,a+10);
	list1.sort();//使用其成员函数版本的算法，排序
	list1.unique();//删除相同元素
	for (auto it1 = list1.begin(); it1 != list1.end(); ++it1)
	{
		cout<<*it1<<" ";
	}
 
	return 0;  
}
```
### 11.12
```
#include<iostream>  
#include<string>  
#include<fstream>
#include<list>
#include<vector> 
#include<map>  
#include<set>
#include<cctype>//ctype无法打开，包含tolower()函数和ispunct函数
#include<algorithm>
#include<utility>//保存pair的头文件
using namespace std;
 
int main(int argc, char**argv)  
{ 
	vector<pair<string,int>> vec1(12);//事先定义大小，或者使用push_back()
	ifstream in1("1.txt");
	string str;
	size_t i = 0;
	while (in1>>str)
	{
		vec1[i].first = str;
		++i;
	}
	ifstream in2("2.txt");
	int val;
	size_t j = 0;
	while (in2>>val)
	{
		vec1[j].second = val;
		++j;
	}
 
	vector<pair<string,int>>::iterator it1 = vec1.begin();
	cout<<"vector中元素为："<<endl;
	for (it1; it1 != vec1.end(); ++it1)
	{
		cout<<it1->first<<" "<<it1->second<<endl;
	}
 
	return 0;  
} 
```
### 11.17

copy(v.begin(),v.end(),inserter(c,c.end()));//正确 
copy(v.begin(),v.end(),back_inserter(c)); //错误 multiset没有push_back这个操作，尾插法不适合 
copy(c.begin(),c.end(),inserter(v,v.end()));//正确 
copy(c.begin(),c.end(),back_inserter(v));//正确

### 11.38
```
#include <iostream>
#include <string>
#include <map>
#include <tr1/unordered_map>  

using namespace std;

int main()
{
    std::tr1::unordered_map<string, size_t> word_count; //string到size_t的空map 
    string word;
    while(cin >> word)
        ++word_count[word];        //提取word的计数器并将其加1 
    for(const auto &w : word_count)
    cout << w.first << " occurs " << w.second << ((w.second > 1) ? " times" : " time") << endl;
    return 0;
} 
```
```
#include <iostream>
#include <vector>
#include <map>
#include <fstream>
#include <string>
#include<sstream>
#include <tr1/unordered_map>

using namespace std;

//建立转换映射 
std::tr1::unordered_map<string, string> buildMap(ifstream &map_file)
{
    std::tr1::unordered_map<string, string> trans_map; //保存转换规则
    string key; //要转换的单词
    string value; //替换后的内容
    //读取第一个单词存入key中，行中剩余内容存入value
    while (map_file >> key && getline(map_file, value)) 
        if(value.size() > 1) //检查是否有转换规则
            trans_map[key] = value.substr(1); //跳过前导空格
        else
            throw runtime_error("no rule for " + key);
    return trans_map; 
 } 

//生成转换文本
const string & transform(const string &s, const std::tr1::unordered_map<string,string> &m)
{
    //实际的转换工作；此部分是程序的核心
    auto map_it = m.find(s);
    //如果单词在转换规则map中
    if (map_it != m.end()) //cend改为end 
        return map_it -> second; //使用替换语句
    else
        return s; //否则返回原string 
} 


/*单词转换程序*/
void word_transform(ifstream &map_file, ifstream &input)
{
    auto trans_map = buildMap(map_file); //保存转换规则
    string text;
    while (getline(input, text)) 
    {
        istringstream stream(text);  //读取每个单词
        string word;
        bool firstword = true;      //控制是否打印空格 
        while (stream >> word){
            if (firstword)
                firstword = false;
            else
                cout << " ";      //在单词间打印一个空格
            //transform 返回它的第一个参数或其他转换之后的形式
            cout << transform(word, trans_map); //打印输出 
        } 
        cout << endl;  //完成一行的转换 
     } 
}



int main()
{

    ifstream file1("map_file.txt"); //转换的规则 
    ifstream file2("data_input.txt"); //输入的文件 
    word_transform(file1, file2);
    return 0;
}
```
### 13.12

析构函数执行三次：accum，item1，item2

### 13.18
```
#include<iostream>  
#include<string>  
#include<fstream>
#include<list>
#include<vector> 
#include<map>  
#include<set>
#include<cctype>//ctype无法打开，包含tolower()函数和ispunct函数
#include<algorithm>
#include<utility>//保存pair的头文件
#include<memory>
using namespace std;
 
//具体操作时将类的声明置于头文件中
class Employee
{
public:
	Employee();//默认构造函数
	Employee(string& s);//接受一个string的构造函数	
	Employee(const Employee&) =delete;//不需要拷贝构造函数，怎么可能有人一样。将其声明为 =delete
	Employee& operator= (const Employee&) =delete;
	int number(){return _number;}
private:
	string employee;
	int _number;
	static int O_number;//static静态成员数据在类内声明，但只可以在类外定义，在类外定义时可不加static
};
 
int Employee::O_number = 0;
Employee::Employee()//默认构造函数
{
	_number = O_number++;
}
Employee::Employee(string& s)//接受一个string的构造函数
{
	employee = s;
	_number = O_number++;
}
 
void show(Employee a)
{
	cout<<a.number()<<endl;
}
int main(int argc, char**argv)  
{
	Employee a, b, c;
	show(a);//调用函数时需要拷贝一次
	show(b);
	show(c);
 
	return 0;
}
```

### 14.3

(a)"cobble" == "stone"应用了C++语言内置版本的==，比较两个指针。

(b)svec1[0] == svec2[0]应用了string版本的重载==。

(c)svec1 = svec2应用了vector版本的重载==。

(d)svec1[0] == "stone"应用了string版本的重载==，字符串字面常量被转换成string。

### 14.20
```
class Sales_data
{
	friend Sales_data operator+(const Sales_data &lhs, const Sales_data &rhs);
public:
	Sales_data& operator+=(const Sales_data &rhs);
	//其他成员
};
Sales_data operator+(const Sales_data &lhs, const Sales_data &rhs)
{
	Sales_data sum = lhs;
	sum += rhs;
	return sum;
}
Sales_data& Sales_data::operator+=(const Sales_data &rhs)
{
	units_sold += rhs.units_sold;
	revenue += rhs.revenue;
	return *this;
}
```
### 15.12

当然有需要，两个不一样的，override是表示对其基类函数的覆盖，final是表示此函数不能再被其他的函数所覆盖

### 15，16
```
class limit_quote : public Disc_quote
{
public:
	limit_quote();//默认构造函数
	limit_quote(const string&book, double price, size_t qty,double disc):Disc_quote(book,price,qty,disc){}//构造函数,直接调用基类的构造函数
	double net_price(size_t n) const
	{
		if (n > quantity)
		{
			return quantity*(1-_discount)*price+(n-quantity)*price;
		} 
		else
		{
			return quantity*(1-_discount)*price;
		}
	}
};
```
