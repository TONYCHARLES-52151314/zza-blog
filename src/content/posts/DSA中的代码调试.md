---
title: DSA中的代码调试
published: 2026-03-29
description: NONE
image: ""
tags:
  - notes
category: DSA
draft: false
---
1.静态阅读代码

---

2.debug

"根据错误数据，输出一些重要的中间变量的值，然后观察是否和预期一样。"  

而不是直接用调试  

无论是平时训练还是比赛中我都建议少用 IDE 断点调试功能和单步调试功能，通常比较浪费时间。  

---

3.对拍

对拍是写完代码后，通过再另写一篇代码来判断自己的代码是否正确，是否能够实现预期功能。  

“一般是写完一篇代码后，再另写一篇绝对正确的代码（但在时间空间复杂度上不一定是最优的），以及一篇随机生成数据的代码，然后将随机生成的数据给到绝对正确的代码以及需要验证的代码。如果两个代码跑出来的输出数据一致，那么就能够在一定程度上说明这个需要验证代码是正确的，而如果跑出来的结果，不一样，那就说明这个需要验证的代码是错误的或者说是有漏洞的。接下来我们的任务就是去debug了，有需要的可以回顾我前面写的有关“调试”的文章。  
————————————————  
版权声明：本文为CSDN博主「Autumn_goose」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。  
原文链接：https://blog.csdn.net/autumn_goose/article/details/130939907”
**三种对拍**：
* 验证一个公式
* 高精度（还没学）
* 检验一个长又复杂的代码是否正确？拿一个短的代码去对拍(还没学)
```cpp
#include<iostream>

using namespace std;

void solve() {

    while(1) {

        system("data.exe > data.txt");

        system("bf.exe < data.txt > bf.txt");

        system("test.exe < data.txt > test.txt");

        if (system("fc test.txt bf.txt")) {

            cout << "wrong!" << endl;

            break;

        }

    }

    system("pause");

}

int main() {

    solve();

    return 0;

}
```
生成随机数的时候可以用这个去申请一块内存，把内存地址转化成unsigned long long，然后加上当前的时间，避免了每次都是一个种子，或者同一秒内的种子是一样的情况
```cpp
srand(time(0) + (unsigned long long)(new char));
```

# 还有一点没懂