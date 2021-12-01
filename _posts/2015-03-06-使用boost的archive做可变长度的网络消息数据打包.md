---
layout: post
title:  使用boost的archive做可变长度的网络消息数据打包
date:   2015-03-06
tags:
      - 随笔
---
::: txtcont
**https://blog.csdn.net/gddsky/article/details/5021388\
**

**\
**

**目的**

在结构体上面可以定义std::string这样的数据，并方便打包与解包

 

**核心**

1.        
boost库提供了非常方便的对像序列化库boost::archive、boost::serialization，通过这两个库我们可以很方便的打包std里面像std::string、std::list这些类型的数据。

2.        
打包数据我们当然要使用二进制的方式所以使用boost::archive::binary_iarchive、boost::archive::binary_oarchive。

3.        
在真正编写代码的过程中发现这两个类为了序列化出来的数据有版本的区分还在输出的数据最前面加上一些版本信息，为了去除它们，最终我的解决方案是重写这两个类，将输出版本信息这块代码给关闭掉。

4.        
boost的序列化方法是在结构体上面做一个函数（全局的不提了），如下：

struct data

{

std::string v;

int v2;

template\<\<span style=\"COLOR: blue\"\>class Archive>

void serialize(Archive & ar, const unsigned int version)

{

    ar & v;

    ar & v2;

}

};

程序员总有一个通病"懒"，看到这样的代码就在想能不能将serialize函数给去掉或者让计算机自动生成，分析了很多，最后的解决方法是使用宏来处理在代码里可以看到MSG1、MSG2...MSG9这样的宏，它们就是为了完成这个想法而做的东西，很丑！！！（哪位有好的解决方法请一定要告知我，非常感谢）

5.        
为了做到使用更加方便，简单做了MsgPack与MsgUnpack类来做打包与解包工作。

 

**代码**

 

****\[cpp\]** [view
plain](https://blog.csdn.net/gddsky/article/details/5021388# "view plain")[copy](https://blog.csdn.net/gddsky/article/details/5021388# "copy")**

1.  **//msg_binary_iarchive.h  **
2.  **#pragma once  **
3.  **// 这文件内容是直接复制的boost的binary_iarchive.hpp的内容做了点儿修改  **
4.  **  **
5.  **#include   **
6.  **  **
7.  **#pragma warning(push)  **
8.  **#pragma warning(disable : 4267)  **
9.  **#pragma warning(disable : 4996)  **
10. **#include   **
11. **#include   **
12. **#include   **
13. **#pragma warning(pop)  **
14. **  **
15. **namespace boost {   **
16. **namespace archive {  **
17. **  **
18. **class naked_binary_iarchive :   **
19. **    public binary_iarchive_impl\<  **
20. **        boost::archive::naked_binary_iarchive,   **
21. **        std::istream::char_type,   **
22. **        std::istream::traits_type  **
23. **    >  **
24. **{  **
25. **public:  **
26. **    naked_binary_iarchive(std::istream & is, unsigned int flags = 0) :  **
27. **        binary_iarchive_impl\<  **
28. **            naked_binary_iarchive, std::istream::char_type, std::istream::traits_type  **
29. **        >(is, flags)  **
30. **    {}  **
31. **    naked_binary_iarchive(std::streambuf & bsb, unsigned int flags = 0) :  **
32. **        binary_iarchive_impl\<  **
33. **            naked_binary_iarchive, std::istream::char_type, std::istream::traits_type  **
34. **        >(bsb, flags)  **
35. **    {}  **
36. **};  **
37. **  **
38. **} // namespace archive  **
39. **} // namespace boost  **
40. **  **
41. **#include   **
42. **  **
43. **namespace boost {   **
44. **namespace archive {  **
45. **  **
46. **class msg_binary_iarchive :   **
47. **    public binary_iarchive_impl\<  **
48. **        boost::archive::msg_binary_iarchive,   **
49. **        std::istream::char_type,   **
50. **        std::istream::traits_type  **
51. **    >,  **
52. **    public detail::shared_ptr_helper  **
53. **{  **
54. **public:  **
55. **    typedef binary_iarchive_impl\<  **
56. **        boost::archive::msg_binary_iarchive,   **
57. **        std::istream::char_type,   **
58. **        std::istream::traits_type  **
59. **    > base;  **
60. **    msg_binary_iarchive(std::istream & is, unsigned int flags = 0) :  **
61. **        binary_iarchive_impl\<  **
62. **            msg_binary_iarchive, std::istream::char_type, std::istream::traits_type  **
63. **        >(is, flags)  **
64. **    {}  **
65. **    msg_binary_iarchive(std::streambuf & bsb, unsigned int flags = 0) :  **
66. **        binary_iarchive_impl\<  **
67. **            msg_binary_iarchive, std::istream::char_type, std::istream::traits_type  **
68. **        >(bsb, flags)  **
69. **    {}  **
70. **  **
71. **    template\<\</span>class T>  **
72. **    void load_override(T & t, BOOST_PFTO int)  **
73. **    {  **
74. **        BOOST_MPL_ASSERT_NOT(( boost::is_pointer ));  **
75. **        base::load_override(t, 0);  **
76. **    }  **
77. **  **
78. **    // 这些信息都不要了  **
79. **    void load_override(boost::archive::class_id_optional_type &, int){}  **
80. **    void load_override(boost::archive::tracking_type & t, int){t.t = false;}  **
81. **    void load_override(boost::archive::version_type & t, int){t.t = 0;}  **
82. **};  **
83. **  **
84. **} // namespace archive  **
85. **} // namespace boost  **
86. **  **
87. **// required by export  **
88. **BOOST_SERIALIZATION_REGISTER_ARCHIVE(boost::archive::msg_binary_iarchive)  **
89. **BOOST_SERIALIZATION_USE_ARRAY_OPTIMIZATION(boost::archive::msg_binary_iarchive)  **

 

****\[cpp\]** [view
plain](https://blog.csdn.net/gddsky/article/details/5021388# "view plain")[copy](https://blog.csdn.net/gddsky/article/details/5021388# "copy")**

1.  **//msg_binary_oarchive.h  **
2.  **#pragma once  **
3.  **// 这文件内容是直接复制的boost的binary_oarchive.hpp的内容做了点儿修改  **
4.  **  **
5.  **#include   **
6.  **  **
7.  **#pragma warning(push)  **
8.  **#pragma warning(disable : 4267)  **
9.  **#pragma warning(disable : 4996)  **
10. **#include   **
11. **#include   **
12. **#include   **
13. **#pragma warning(pop)  **
14. **  **
15. **namespace boost {   **
16. **namespace archive {  **
17. **  **
18. **class msg_binary_oarchive :   **
19. **    public binary_oarchive_impl\<  **
20. **        msg_binary_oarchive, std::ostream::char_type, std::ostream::traits_type  **
21. **    >  **
22. **{  **
23. **public:  **
24. **    typedef binary_oarchive_impl\<  **
25. **        msg_binary_oarchive, std::ostream::char_type, std::ostream::traits_type  **
26. **    > base;  **
27. **    msg_binary_oarchive(std::ostream & os, unsigned int flags = 0) :  **
28. **        binary_oarchive_impl\<  **
29. **            msg_binary_oarchive, std::ostream::char_type, std::ostream::traits_type  **
30. **        >(os, flags)  **
31. **    {}  **
32. **    msg_binary_oarchive(std::streambuf & bsb, unsigned int flags = 0) :  **
33. **        binary_oarchive_impl\<  **
34. **            msg_binary_oarchive, std::ostream::char_type, std::ostream::traits_type  **
35. **        >(bsb, flags)  **
36. **    {}  **
37. **  **
38. **    template\<\</span>class T>  **
39. **    void save_override(T & t, BOOST_PFTO int)  **
40. **    {  **
41. **        BOOST_MPL_ASSERT_NOT(( boost::is_pointer ));  **
42. **        base::save_override(t, 0);  **
43. **    }  **
44. **  **
45. **    // 这些信息都不要了  **
46. **    void save_override(const boost::archive::class_id_optional_type &, int){}  **
47. **    void save_override(const boost::archive::tracking_type &, int){}  **
48. **    void save_override(const boost::archive::version_type &, int){}  **
49. **  **
50. **};  **
51. **  **
52. **typedef msg_binary_oarchive naked_binary_oarchive;  **
53. **  **
54. **} // namespace archive  **
55. **} // namespace boost  **
56. **  **
57. **// required by export  **
58. **BOOST_SERIALIZATION_REGISTER_ARCHIVE(boost::archive::msg_binary_oarchive)  **
59. **BOOST_SERIALIZATION_USE_ARRAY_OPTIMIZATION(boost::archive::msg_binary_oarchive)  **

 

****\[cpp\]** [view
plain](https://blog.csdn.net/gddsky/article/details/5021388# "view plain")[copy](https://blog.csdn.net/gddsky/article/details/5021388# "copy")**

1.  **//MsgBinaryArchive.h  **
2.  **#pragma once  **
3.  **  **
4.  **#include   **
5.  **#include \"boost/serialization/string.hpp\"  **
6.  **#include \"boost/serialization/list.hpp\"  **
7.  **#include \"msg_binary_iarchive.h\"  **
8.  **#include \"msg_binary_oarchive.h\"  **
9.  **  **
10. **#define MSG1(mn,t1,n1)/  **
11. **    struct mn/  **
12. **    {/  **
13. **        t1 vn1;/  **
14. **        template\<\</span>class Archive> void serialize(Archive & ar, const unsigned int version)/  **
15. **        {/  **
16. **            ar & vn1;/  **
17. **        }/  **
18. **    };  **
19. **  **
20. **#define MSG2(mn,t1,n1,t2,n2)/  **
21. **    struct mn/  **
22. **    {/  **
23. **        t1 n1;/  **
24. **        t2 n2;/  **
25. **        template\<\</span>class Archive> void serialize(Archive & ar, const unsigned int version)/  **
26. **        {/  **
27. **            ar & n1;/  **
28. **            ar & n2;/  **
29. **        }/  **
30. **    };  **
31. **  **
32. **#define MSG3(mn,t1,n1,t2,n2,t3,n3)/  **
33. **    struct mn/  **
34. **    {/  **
35. **        t1 n1;/  **
36. **        t2 n2;/  **
37. **        t3 n3;/  **
38. **        template\<\</span>class Archive> void serialize(Archive & ar, const unsigned int version)/  **
39. **        {/  **
40. **            ar & n1;/  **
41. **            ar & n2;/  **
42. **            ar & n3;/  **
43. **        }/  **
44. **    };  **
45. **  **
46. **#define MSG4(mn,t1,n1,t2,n2,t3,n3,t4,n4)/  **
47. **    struct mn/  **
48. **    {/  **
49. **        t1 n1;/  **
50. **        t2 n2;/  **
51. **        t3 n3;/  **
52. **        t4 n4;/  **
53. **        template\<\</span>class Archive> void serialize(Archive & ar, const unsigned int version)/  **
54. **        {/  **
55. **            ar & n1;/  **
56. **            ar & n2;/  **
57. **            ar & n3;/  **
58. **            ar & n4;/  **
59. **        }/  **
60. **    };  **
61. **  **
62. **#define MSG5(mn,t1,n1,t2,n2,t3,n3,t4,n4,t5,n5)/  **
63. **    struct mn/  **
64. **    {/  **
65. **        t1 n1;/  **
66. **        t2 n2;/  **
67. **        t3 n3;/  **
68. **        t4 n4;/  **
69. **        t5 n5;/  **
70. **        template\<\</span>class Archive> void serialize(Archive & ar, const unsigned int version)/  **
71. **        {/  **
72. **            ar & n1;/  **
73. **            ar & n2;/  **
74. **            ar & n3;/  **
75. **            ar & n4;/  **
76. **            ar & n5;/  **
77. **        }/  **
78. **    };  **
79. **  **
80. **#define MSG6(mn,t1,n1,t2,n2,t3,n3,t4,n4,t5,n5,t6,n6)/  **
81. **    struct mn/  **
82. **    {/  **
83. **        t1 n1;/  **
84. **        t2 n2;/  **
85. **        t3 n3;/  **
86. **        t4 n4;/  **
87. **        t5 n5;/  **
88. **        t6 n6;/  **
89. **        template\<\</span>class Archive> void serialize(Archive & ar, const unsigned int version)/  **
90. **        {/  **
91. **            ar & n1;/  **
92. **            ar & n2;/  **
93. **            ar & n3;/  **
94. **            ar & n4;/  **
95. **            ar & n5;/  **
96. **            ar & n6;/  **
97. **        }/  **
98. **    };  **
99. **  **
100. **#define MSG7(mn,t1,n1,t2,n2,t3,n3,t4,n4,t5,n5,t6,n6,t7,n7)/  **
101. **    struct mn/  **
102. **    {/  **
103. **        t1 n1;/  **
104. **        t2 n2;/  **
105. **        t3 n3;/  **
106. **        t4 n4;/  **
107. **        t5 n5;/  **
108. **        t6 n6;/  **
109. **        t7 n7;/  **
110. **        template\<\</span>class Archive> void serialize(Archive & ar, const unsigned int version)/  **
111. **        {/  **
112. **            ar & n1;/  **
113. **            ar & n2;/  **
114. **            ar & n3;/  **
115. **            ar & n4;/  **
116. **            ar & n5;/  **
117. **            ar & n6;/  **
118. **            ar & n7;/  **
119. **        }/  **
120. **    };  **
121. **  **
122. **#define MSG8(mn,t1,n1,t2,n2,t3,n3,t4,n4,t5,n5,t6,n6,t7,n7,t8,n8)/  **
123. **    struct mn/  **
124. **    {/  **
125. **        t1 n1;/  **
126. **        t2 n2;/  **
127. **        t3 n3;/  **
128. **        t4 n4;/  **
129. **        t5 n5;/  **
130. **        t6 n6;/  **
131. **        t7 n7;/  **
132. **        t8 n8;/  **
133. **        template\<\</span>class Archive> void serialize(Archive & ar, const unsigned int version)/  **
134. **        {/  **
135. **            ar & n1;/  **
136. **            ar & n2;/  **
137. **            ar & n3;/  **
138. **            ar & n4;/  **
139. **            ar & n5;/  **
140. **            ar & n6;/  **
141. **            ar & n7;/  **
142. **            ar & n8;/  **
143. **        }/  **
144. **    };  **
145. **  **
146. **#define MSG9(mn,t1,n1,t2,n2,t3,n3,t4,n4,t5,n5,t6,n6,t7,n7,t8,n8,t9,n9)/  **
147. **    struct mn/  **
148. **    {/  **
149. **        t1 n1;/  **
150. **        t2 n2;/  **
151. **        t3 n3;/  **
152. **        t4 n4;/  **
153. **        t5 n5;/  **
154. **        t6 n6;/  **
155. **        t7 n7;/  **
156. **        t8 n8;/  **
157. **        t9 n9;/  **
158. **        template\<\</span>class Archive> void serialize(Archive & ar, const unsigned int version)/  **
159. **        {/  **
160. **            ar & n1;/  **
161. **            ar & n2;/  **
162. **            ar & n3;/  **
163. **            ar & n4;/  **
164. **            ar & n5;/  **
165. **            ar & n6;/  **
166. **            ar & n7;/  **
167. **            ar & n8;/  **
168. **            ar & n9;/  **
169. **        }/  **
170. **    };  **
171. **  **
172. **class MsgPack  **
173. **{  **
174. **public:  **
175. **    MsgPack():  **
176. **        \_oa(\_os, boost::archive::no_header)  **
177. **    {}  **
178. **  **
179. **    template \<\</span>class T>  **
180. **    MsgPack& operator & (const T & v)  **
181. **    {  **
182. **        reset();  **
183. **        \_oa & v;  **
184. **  **
185. **        return \*this;  **
186. **    }  **
187. **  **
188. **    template \<\</span>class T>  **
189. **    MsgPack& operator \<\< (const T & v)  **
190. **    {  **
191. **        \_oa & v;  **
192. **  **
193. **        return \*this;  **
194. **    }  **
195. **  **
196. **    void reset()  **
197. **    {  **
198. **        \_os.freeze(false);  **
199. **        \_os.seekp(0);  **
200. **        \_os.seekg(0);  **
201. **    }  **
202. **  **
203. **    const char\* buffer()  **
204. **    {  **
205. **        return \_os.str();  **
206. **    }  **
207. **  **
208. **    size_t size()  **
209. **    {  **
210. **        return \_os.pcount();  **
211. **    }  **
212. **  **
213. **private:  **
214. **    std::strstream                          \_os;  **
215. **    boost::archive::msg_binary_oarchive     \_oa;  **
216. **};  **
217. **  **
218. **class MsgUnpack  **
219. **{  **
220. **public:  **
221. **    MsgUnpack():  **
222. **        \_ia(\_is, boost::archive::no_header)  **
223. **    {}  **
224. **  **
225. **    void reset(const char\* buf, size_t size)  **
226. **    {  **
227. **        if (\_is.pcount())  **
228. **        {  **
229. **            \_is.seekp(0);  **
230. **            \_is.seekg(0);  **
231. **        }  **
232. **        \_is.write(buf, (std::streamsize)size);  **
233. **    }  **
234. **  **
235. **    template \<\</span>class T>  **
236. **    MsgUnpack& operator >\> (T & v)  **
237. **    {  **
238. **        \_ia & v;  **
239. **  **
240. **        return \*this;  **
241. **    }  **
242. **  **
243. **private:  **
244. **    std::strstream                          \_is;  **
245. **    boost::archive::msg_binary_iarchive     \_ia;  **
246. **};  **
247. **  **
248. **  **
249. **  **
250. ** \
     **
:::
