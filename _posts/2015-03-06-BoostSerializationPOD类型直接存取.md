---
layout: post
title:  "BoostSerializationPOD类型直接存取"
date:   2015-03-06
tags:
      - it
---


https://blog.163.com/ecy_fu/blog/static/4445126200911291022174/



  POD即\"Plain Old Data\"，
它表示一种简单的类型，这种类型没有使用指针，可以直接在两个对象之间直接赋值而没有什么隐患。系统中会存在大量的POD类型，比如保存程序运行的各种状
态参数的结构体，比如表示坐标点的简单类，这些POD类型在序列化时是可以直接进行内存存取的，这样不仅节省空间，而且效率也更高。

        Boost Serialization库提供多种方法以直接保存POD类型。在看Boost
Serialization官 方文档中，我最早看到Bitwise
Serialization这个traits，我以为使用\"BOOST_IS_BITWISE_SERIALIZABLE(my_class)\"声明一
下，以后遇到形如\"ar&
my_class\"将直接进行内存拷贝，这是最理想的使用方法，可是这样用是通不过编译的，编译时会提示my_class不存在serialize方法。
在网上搜了半天也没有发现有使用Bitwise
Serialization比较好的例子。不过天无绝人之路，Boost
Serialization提供了别的接口可以方便直接存取POD类型。
      
第一种方法是使用text_oarchive_impl::save_binary，text_iarchive_impl::load_binary
可以保存一个内存块。因为调用两个函数的时机不同，一个在保存时调用，一个在恢复时调用，因此需要使用一个标志加于判
断，interface_iarchive提供连个标志is_loading和is_saving可以用于判断archive用于保存还是载入。实践证
明，只能使用BOOST_SERIALIZATION_SPLIT_MEMBER宏声明将serialize分离成save和load，否则编译会失败，
因为save_binary属于load_binary属于不同的类方法。
       第二种方法是一种更好的方法。Serialization Wrappers提供Binary
Objects可用于包装POD类型，然后通过对内存直接存取来序列化Binary
Objects。第一种方法还需要判断archive的用途，这种方法不需要判断，代码将更加简洁。
       下面给出一个使用的例子程序，代码如下：
#include
#include
#include

using namespace std;

#include
#include

#include
#include

//该类是典型的POD类型
class Position
{
public:
    double m_lat;
    double m_lon;
    double m_height;

    Position()
    {
    };

    Position(double lon, double lat, double hi)
    {
        m_lon = lon;
        m_lat = lat;
        m_height = hi;
    }
};

class Test
{
public:
    void SetPosition(const Position& pos)
    {
        position = pos;
    }

    Test()
    {
    };

    template
    void save(Archive & ar, const unsigned int version) const
    {
        //对于POD类型直接序列化
        ar.save_binary(&position, sizeof(Position));
    }

    template
    void load(Archive & ar, const unsigned int version)
    {
        ar.load_binary(&position, sizeof(Position));
    }

    //将save和load分开
    BOOST_SERIALIZATION_SPLIT_MEMBER()

public:
    Position position;
};

int main()
{
    Test test;

    Position pos(104.12, 40.23, 1000);

    test.SetPosition(pos);

    {
        ofstream ofs(\"test.dat\");

        boost::archive::text_oarchive oa(ofs);

        oa \<\< test;
    }
   
    {
        ifstream ifs(\"test.dat\");

        boost::archive::text_iarchive ia(ifs);

        Test test2;

        ia \>\> test2;

        cout \<\< test2.position.m_lon \<\< endl;
    }

};
       使用第二种方法只需使用下面这段代码替换上面Test类的声明即可。
class Test
{
public:
    void SetPosition(const Position& pos)
    {
        position = pos;
    }

    Test()
    {
    };

    template
    void serialize(Archive & ar, const unsigned int version)
    {
        //优势非常明显
        ar& boost::serialization::binary_object((void\*)&position,
sizeof(Position));
    }

public:
    Position position;
};



