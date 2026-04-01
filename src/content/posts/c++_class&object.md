---
title: 类和对象
published: 2026-03-16
description: c++ class&object
image: ''
tags: [c++]
category: 学习笔记
draft: false 
---
结构体->类

private 不能调用

成员函数的定义可以在类内部也可以在外部（只在内部做声明） 

inline function内部直接定义或者在外部写了inline的函数
```
class Student
{
    void print();
}
main()
{
    inline Student::print()
}
```

建议 相对简捷的函数放在类的里面，复杂的可以放在类的外面

## c++分文件编写
student.hpp     定义类的结构

作用：目录，告诉编译器有Student这个类，里面有哪些变量和功能

细节：#prgma once

student.cpp     实现类的成员函数具体逻辑

细节：#include "student.hpp"
函数名前面要加Student::

main.cpp        主程序

细节：#include "student.hpp"
在主程序里面可以去创建类的对象，调用类的方法

**cmake使用**

**//tolearn**
不过大概知道了cmake是用来管理多文件项目的东西，可以开一个文件夹叫build，然后在这个文件夹里面敲```cmake ..```然后就会产生makefile文件，然后打make就行了

## 构造函数和析构函数
创建对象：申请内存+构造函数（这一步在c里面没有）

没有定义构造函数，会默认一个空的构造函数

### 构造函数：名字和构造函数相同

常用于对于对象内部成员进行初始化

成员变量可以用：进行初始化

也可以直接赋值

**constructor.cpp**
```
#include <iostream>
#include <cstring>

using namespace std;

class Student
{
  private:
    char name[4];
    int born;
    bool male; 
  public:
    //第一个构造函数
    Student()
    {
        name[0] = '\0';
        born = 0;
        male = false;
        cout << "Constructor: Person()" << endl;//用于测试到底有没有被调用
    }

    //第二个构造函数，接受一个参数，born(0),male(false)属于是列表初始化
    Student(const char * initName): born(0), male(false)
    {
        setName(initName);
        cout << "Constructor: Person(const char*)" << endl;
    }

    //第三个构造函数
    Student(const char * initName, int initBorn, bool isMale)
    {
        setName(initName);
        born = initBorn;
        male = isMale;
        cout << "Constructor: Person(const char, int , bool)" << endl;
    }
    

    void setName(const char * s)
    {
        if (s == NULL)
        {
            std::cerr << "The input is NULL." << std::endl;
            return;
        }
        size_t len = sizeof(name) - 1;
        strncpy(name, s, len);
        name[len] = '\0';
    }

    void setBorn(int b)
    {
        if (b >= 1990 && b <= 2020 )
            born = b;
        else
            std::cerr << "The input b is " << b << ", and should be in [1990, 2020]." << std::endl;
    }

    // 仅仅在此声明
    void setGender(bool isMale);
    void printInfo();
};

void Student::setGender(bool isMale)
{
    male = isMale;
}
void Student::printInfo()
{
    std::cout << "Name: " << name << std::endl;
    std::cout << "Born in " << born << std::endl;
    std::cout << "Gender: " << (male ? "Male" : "Female") << std::endl;
}

int main()
{
    //人为置空的构造函数
    Student yu;//创建对象，创建在栈上面
    yu.printInfo();

    yu.setName("Yu");
    yu.setBorn(2000);
    yu.setGender(true);
    yu.printInfo();

    //使用第二种构造函数
    Student li("li");
    li.printInfo();

    //第三种构造函数，传入三个参数
    Student xue = Student("XueQikun", 1962, true);
    // 等价于Student xue  ("XueQikun", 1962, true);
    // //a question: what will happen since "XueQikun" has 4+ characters?
    xue.printInfo();

    //分配在堆区
    Student * zhou =  new Student("Zhou", 1991, false);
    zhou->printInfo();
    delete zhou;

    Student * s =  new Student();
    // Student * s =  new Student; // the same
    s->printInfo();
    delete s;

    //由于 zhou 和 s 的数据类型是指针（Student *），语法规定必须使用箭头运算符 -> 来解引用并调用其成员函数。
    return 0;
}
```
### 析构函数
销毁对象钱调用的函数
析构函数名：~类名
不可以重载，只有一个
```
class Student
{
    //...
    public:
        Student()
        {
            name=new char[1024]{0};
            born=0;
            male=false;
            cout<<"Constructor:Person()"<<endl;
        }
        ~Student()
        {
            delete [] name;
        }
}
```

**destructor.cpp**
```
#include <iostream>
#include <cstring>

using namespace std;

class Student
{
  private:
    char * name;
    int born;
    bool male; 
  public:
    Student()
    {
        name = new char[1024]{0};
        born = 0;
        male = false;
        cout << "Constructor: Person()" << endl;
    }
    Student(const char * initName, int initBorn, bool isMale)
    {
        name =  new char[1024];
        setName(initName);
        born = initBorn;
        male = isMale;
        cout << "Constructor: Person(const char, int , bool)" << endl;
    }
    ~Student()
    {
        cout << "To destroy object: " << name << endl;
        delete [] name;
    }

    void setName(const char * s)
    {
        if (s == NULL)
        {
            std::cerr << "The input is NULL." << std::endl;
            return;
        }
        size_t len = 1024 - 1;
        strncpy(name, s, len);
        name[len] = '\0';
    }
    void setBorn(int b)
    {
        if (b >= 1990 && b <= 2020 )
            born = b;
        else
            std::cerr << "The input b is " << b << ", and should be in [1990, 2020]." << std::endl;
    }
    // the declarations, the definitions are out of the class
    void setGender(bool isMale);
    void printInfo();
};

void Student::setGender(bool isMale)
{
    male = isMale;
}
void Student::printInfo()
{
    std::cout << "Name: " << name << std::endl;
    std::cout << "Born in " << born << std::endl;
    std::cout << "Gender: " << (male ? "Male" : "Female") << std::endl;
}

int main()
{
    {
        Student yu;
        yu.printInfo();

        yu.setName("Yu");
        yu.setBorn(2000);
        yu.setGender(true);
        yu.printInfo();
    }
    //这里的一对“{}”表示一个作用域，一个作用域结束就会把作用域内部建立在栈区的对象销毁，这样就会看见yu在第一个被销毁，否则就是在最后一个销毁了

    Student xue = Student("XueQikun", 1962, true);
    xue.printInfo();

    Student * zhou =  new Student("Zhou", 1991, false);
    zhou->printInfo();
    delete zhou;
    //堆上的要手动销毁，防止数据泄露

    return 0;
}
```


**```delete name;和delete [] name;``` 区别**


**array.cpp**
```
#include <iostream>
#include <cstring>

using namespace std;

class Student
{
  private:
    char * name;
    int born;
    bool male; 
  public:
    Student()
    {
        name = new char[1024]{0};
        born = 0;
        male = false;
        cout << "Constructor: Person()" << endl;
    }
    Student(const char * initName, int initBorn, bool isMale)
    {
        name =  new char[1024];
        setName(initName);
        born = initBorn;
        male = isMale;
        cout << "Constructor: Person(const char, int , bool)" << endl;
    }
    ~Student()
    {
        cout << "To destroy object: " << name << endl;
        delete [] name;
    }

    void setName(const char * s)
    {
        if (s == NULL)
        {
            std::cerr << "The input is NULL." << std::endl;
            return;
        }
        size_t len = 1024 - 1;
        strncpy(name, s, len);
        name[len] = '\0';
    }
    void setBorn(int b)
    {
        if (b >= 1990 && b <= 2020 )
            born = b;
        else
            std::cerr << "The input b is " << b << ", and should be in [1990, 2020]." << std::endl;
    }
    // the declarations, the definitions are out of the class
    void setGender(bool isMale);
    void printInfo();
};

void Student::setGender(bool isMale)
{
    male = isMale;
}
void Student::printInfo()
{
    std::cout << "Name: " << name << std::endl;
    std::cout << "Born in " << born << std::endl;
    std::cout << "Gender: " << (male ? "Male" : "Female") << std::endl;
}

int main()
{
    Student * class1 = new Student[3]{
        {"Tom", 2000, true},
        {"Bob", 2001, true},
        {"Amy", 2002, false},
    };

    class1[1].printInfo();
    delete class1;
    // delete []class1;


    return 0;
}
```

## this指针
this 指针是一个特殊的指针，它指向当前对象的实例。

每一个对象都能通过 this 指针来访问自己的地址。

主要是明确地告诉编译器我们想要访问当前对象的成员变量，而不是函数参数或局部变量

```
#include <iostream>
#include <cstring>

using namespace std;

class Student
{
  private:
    char * name;
    int born;
    bool male; 
  public:
    Student()
    {
        name = new char[1024]{0};
        born = 0;
        male = false;
        cout << "Constructor: Person()" << endl;
    }
    Student(const char * initName, int initBorn, bool isMale)
    {
        name =  new char[1024];
        setName(initName);
        born = initBorn;
        male = isMale;
        cout << "Constructor: Person(const char, int , bool)" << endl;
        cout << "this = " << static_cast<void *>(this) << endl;        
    }
    ~Student()
    {
        cout << "To destroy object: " << name << endl;
        delete [] name;
    }

    void setName(const char * s)
    {
        if (s == NULL)
        {
            std::cerr << "The input is NULL." << std::endl;
            return;
        }
        size_t len = 1024 - 1;
        strncpy(name, s, len);
        name[len] = '\0';
    }
    void setBorn(int b)
    {
        if (b >= 1990 && b <= 2020 )
            born = b;
        else
            std::cerr << "The input b is " << b << ", and should be in [1990, 2020]." << std::endl;
    }
    // the declarations, the definitions are out of the class
    void setGender(bool isMale);
    void printInfo();
};

void Student::setGender(bool isMale)
{
    male = isMale;
}
void Student::printInfo()
{
    std::cout << "Name: " << name << std::endl;
    std::cout << "Born in " << born << std::endl;
    std::cout << "Gender: " << (male ? "Male" : "Female") << std::endl;
}

int main()
{
    Student * s = new Student("Tom", 2000, true);
    cout << "s = " << static_cast<void *>(s) << endl;
    return 0;
}
```

## 类里面的const和static
C语言中推崇宏

### const
c++里面喜欢用const 

const int * p_int;  不能透过指针去修改所指内容

int const * p_int;  不能透过指针去修改所指内容

int * const p_int;  不能改变所指的变量，（箭头方向不变）

```
int getBorn() const
{
    born++; //Can it be modified?
    return born;
}
const 函数const写在后面
然后const的限制是成员函数不能改变
```

### static
静态成员属于整个类，而不是属于某个被创建出来的具体对象。
所有对象共享这一个变量
```
#include <iostream>
#include <cstring>

using namespace std;

class Student
{
  private:
    static size_t student_total; // declaration only
    //inline static size_t student_total = 0; //C++17, 不需要专门到外面去定义了
    char * name;
    int born;
    bool male; 
  public:
    Student()
    {
        student_total++;
        name = new char[1024]{0};
        born = 0;
        male = false;
        cout << "Constructor: Person(): student_total = " << student_total << endl;
    }
    Student(const char * initName, int initBorn, bool isMale)
    {
        student_total++;
        name =  new char[1024];
        setName(initName);
        born = initBorn;
        male = isMale;
        cout << "Constructor: Person(const char, int , bool): student_total = " << student_total << endl;
    }
    ~Student()
    {
        student_total--;
        cout << "To destroy object: " << name ;
        cout << ". Then " << student_total << " students are left" << endl;
        delete [] name;
    }

    void setName(const char * s)
    {
        if (s == NULL)
        {
            std::cerr << "The input is NULL." << std::endl;
            return;
        }
        size_t len = 1024 - 1;
        strncpy(name, s, len);
        name[len] = '\0';        
    }
    void setBorn(int b)
    {
        if (b >= 1990 && b <= 2020 )
            born = b;
        else
            std::cerr << "The input b is " << b << ", and should be in [1990, 2020]." << std::endl;
    }
    static size_t getTotal() {return student_total;}
    // the declarations, the definitions are out of the class
    void setGender(bool isMale);
    void printInfo();
};

void Student::setGender(bool isMale)
{
    male = isMale;
}
void Student::printInfo()
{
    std::cout << "Name: " << name << std::endl;
    std::cout << "Born in " << born << std::endl;
    std::cout << "Gender: " << (male ? "Male" : "Female") << std::endl;
}

size_t Student::student_total = 0; // definition it here

int main()
{
    cout << "---We have " << Student::getTotal() << " students---" << endl;

    Student * class1 = new Student[3]{
        {"Tom", 2000, true},
        {"Bob", 2001, true},
        {"Amy", 2002, false},
    };

    cout << "---We have " << Student::getTotal() << " students---" << endl;

    Student yu("Yu", 2000, true);

    cout << "---We have " << yu.getTotal() << " students---" << endl;

    class1[1].printInfo();
    delete []class1;

    cout << "---We have " << Student::getTotal() << " students---" << endl;

    return 0;
}
/*
*静态成员函数（getTotal()）是没有 this 指针的。它就像是一个挂靠在 Student 这个命名空间下的“全局函数”。它非常纯粹，只能访问类里的静态变量（比如 *student_total），不能访问普通的成员变量（比如它不能 return name;，因为它根本不知道是 Tom 的 name 还是 Bob 的 name）。
*总结：要访问只属于类的变量，就用只属于类的静态函数，并且直接用 类名:: 来召唤它们。*/
```
//todo:复习笔记         lab09        看菜鸟教程

