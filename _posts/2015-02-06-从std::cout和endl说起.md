---
layout: post
title:  "从std::cout和endl说起"
date:   2015-02-06
tags:
      - 随笔
---


https://www.cnblogs.com/lihaozy/archive/2012/05/09/2491791.html
https://blog.csdn.net/hityct1/article/details/4082832


## 正文

         问题是这样的......

相信下面这个程序凡是会写C++程序的同仁都认得，估计学会的第一个C++程序就是它了吧：


//\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--
//        水之真谛 // https://blog.csdn.net/FantasiaX
//\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--
#include int main(int argc, char \*argv\[\]) {          std::cout \<\<
\"Hello, World.\" \<\<
std::[endl](https://soft.zdnet.com.cn/files/list-0-0-139506-1-1.htm "endl");
         return 0; }
        

        
我会写一点C语言的程序，于是在写这个程序的时候就对很多东西"想当然"了。比如对于操作符"\<\<"，在心里一直是与C语言的printf()函数对应起来的------认为它就是封装进了ostream对象中的printf()函数。既然是这样，那么对于"[endl](https://soft.zdnet.com.cn/files/list-0-0-139506-1-1.htm "endl")"，自然就"想当然"地认为它是"n"了。

         突然有一天，在Visual
Studio弹出的代码自动完成窗口中发现，[endl](https://soft.zdnet.com.cn/files/list-0-0-139506-1-1.htm "endl")不是一个成员变量（如果它代表一个字符，那么理应是一个字符类型的成员变量）而是一个成员函数！大脑中立刻蹦出一个解释：或许[endl](https://soft.zdnet.com.cn/files/list-0-0-139506-1-1.htm "endl")函数的返回值是字符"n"吧？可是这个答案存活了不到一秒钟就被否定了------如果想让一个函数执行从而得到它的返回值，应该是调用这个函数，所以写法应该是"std::[endl](https://soft.zdnet.com.cn/files/list-0-0-139506-1-1.htm "endl")()"而不是"std::[endl](https://soft.zdnet.com.cn/files/list-0-0-139506-1-1.htm "endl")"。写成"std::[endl](https://soft.zdnet.com.cn/files/list-0-0-139506-1-1.htm "endl")"是将函数名放在这里,并不是在调用这个函数。哈\~\~脑子里的概念开始互相打架了\~\~

 

        
因为问题是出在了[endl](https://soft.zdnet.com.cn/files/list-0-0-139506-1-1.htm "endl")上，所以一直在查[endl](https://soft.zdnet.com.cn/files/list-0-0-139506-1-1.htm "endl")的定义------结果除了发现MSDN里有个Bug之外，一无所获L

**MSDN里是这样声名的：**

template class\<\_Elem, \_Tr> basic_ostream\<\_Elem, \_Tr>&
[endl](https://soft.zdnet.com.cn/files/list-0-0-139506-1-1.htm "endl")(
basic_ostream\<\_Elem, \_Tr>& \_Ostr );

红色标记的地方写错了:p

**C++ ISO文档里是这样声名的：**

template basic_ostream&
[endl](https://soft.zdnet.com.cn/files/list-0-0-139506-1-1.htm "endl")(basic_ostream&
os);

按MSDN里模板的"写法"根本编译不过去，呵呵。

         不过，MSDN里的说明还是非常有用的------Terminates a line and
flushes the buffer.
可是函数的功能是"结束一行并冲洗缓冲区"，如果想执行这个功能，应该是调用这个函数、应该写[endl](https://soft.zdnet.com.cn/files/list-0-0-139506-1-1.htm "endl")()而不是[endl](https://soft.zdnet.com.cn/files/list-0-0-139506-1-1.htm "endl")啊......看来问题又绕回去了。于是这事儿就放下了。

        
今天遇到高手Sidney，又问起了这个问题。Sidney是研究过这个问题的，虽然没有给出我答案，但他提到这么一句话------"\<\<"操作符是被重载过的，可以接收一个函数作为参数。正好前几天我在写《深入浅出话回调》的时候写过类似的程序，经Sidney一点拨，顿时感觉豁然开朗。很快问题的答案就找到了------

1.  1.         先查看的成员，找到一个全局对象cout
2.  2.         查看cout对象，发现它是ostream的一个实例
3.  3.        
    查看文件说明中的"\<\<"操作符，有10个重载，但是没有可将函数作为参数的
4.  4.        
    仔细想了想，会不会是从别处继承来的呢？（操作符其实就是简写了的函数，完全可以当函数来对待）
5.  5.         查看MSDN，发现ostream是由类模板basic_ostream >生成的
6.  6.        
    查看basic_ostream >的说明，发现它也具有"\<\<"操作符，并且有15个重载。
7.  7.         其中的一个卸载形式是------ basic_ostream& operator \<\< (
    basic_ostream& (\*\_Pfn)(basic_ostream&) );
    说明cout的\<\<操作符可以接受一个函数指针（函数的地址）作为参数。
    这个重载正好与[endl](https://soft.zdnet.com.cn/files/list-0-0-139506-1-1.htm "endl")函数的声名相匹配，所以\<\<后面是可以跟着[endl](https://soft.zdnet.com.cn/files/list-0-0-139506-1-1.htm "endl")的，也就是说，cout对象的\<\<操作符接受到[endl](https://soft.zdnet.com.cn/files/list-0-0-139506-1-1.htm "endl")函数的地址后会在后台调用[endl](https://soft.zdnet.com.cn/files/list-0-0-139506-1-1.htm "endl")函数，而[endl](https://soft.zdnet.com.cn/files/list-0-0-139506-1-1.htm "endl")函数会结束当前行并冲洗buffer。

最后啰嗦一句------你可能会问：不是函数指针吗？为什么不写"std::cout\<\<&[endl](https://soft.zdnet.com.cn/files/list-0-0-139506-1-1.htm "endl")"而写"std::cout\<\<[endl](https://soft.zdnet.com.cn/files/list-0-0-139506-1-1.htm "endl")"呢？实际上，函数名本身就代表的是函数的地址，&[endl](https://soft.zdnet.com.cn/files/list-0-0-139506-1-1.htm "endl")与[endl](https://soft.zdnet.com.cn/files/list-0-0-139506-1-1.htm "endl")的值是一样的J

不信你试试下面的代码，结果与上面的一样：

//\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--
//        水之真谛 // https://blog.csdn.net/FantasiaX
//\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--
#include int main(int argc, char \*argv\[\]) {          std::cout \<\<
\"Hello, World.\" \<\<
&std::[endl](https://soft.zdnet.com.cn/files/list-0-0-139506-1-1.htm "endl");
         return 0; }





myend函数就是模拟endl函数的。它是个全局函数。为了达到与endl一样的用法，还定义了：

mystream& operator\<\<(mystream& (\*fun)(mystream&) );

**\[cpp\]** [view
plain](https://blog.csdn.net/hityct1/article/details/4082832# "view plain")[copy](https://blog.csdn.net/hityct1/article/details/4082832# "copy")

1.  #include \"stdafx.h\"//非vc编译器可去掉  
2.  #include   
3.  #include   
4.  #include   
5.    
6.  using namespace std;  
7.    
8.  class mystream  
9.  {   
10. private:   
11.     stringstream \_ss;  
12.     string \_str;   
13. public:  
14.     friend mystream& myend(mystream& ms); //5  
15.     friend ostream& operator \<\<(ostream& os, const mystream& ms); //6  
16. public:  
17.     mystream& operator\<\< (const int& val ) //1  
18.     {  
19.         \_ss \<\<val;  
20.         return \*this;  
21.     }  
22.   
23.     mystream& operator\<\< (const char\* s ) //2  
24.     {  
25.         \_ss \<\<s;  
26.         return \*this;  
27.     }  
28.   
29.     mystream& operator\<\< (const double& val ) //3  
30.     {  
31.         \_ss \<\<val;  
32.         return \*this;  
33.     }  
34.   
35.     mystream& operator\<\<(mystream& (\*fun)(mystream&) ); //4  
36. };   
37.   
38. mystream& myend(mystream& ms) //这个是模仿std::endl函数  
39. {     
40.     ms.\_str = ms.\_ss.str(); //没做什么，仅仅是给ms.\_str赋值。  
41.   
42.     return ms;  
43. }  
44.   
45. ostream& operator \<\<(ostream& os, const mystream& ms)  
46. {  
47.     os\<\<ms.\_str;  
48.     return os;  
49. }  
50.   
51. mystream& mystream::operator\<\<(mystream& (\*fun)(mystream&) )  
52. {     
53.         assert(NULL != fun);  
54.         return ((\*fun)(\*this));  
55. }  
56.   
57. int main()  
58. {  
59.     mystream mystr;  
60.    
61.     mystr \<\< 5\<\< \"abc\" \<\< 3.57\<\< myend; //调用1，2，3；然后调用4，而4又调用5  
62.     cout \<\< mystr\<\<endl; //输出mystr.\_str到屏幕，调用6  
63.   
64.     return 0;  
65. }  

 




