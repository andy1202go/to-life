### 缘由
很简单，用svn合base，出现了各种各样奇怪的问题，虽然最终没有造成什么大的线上问题，但过程也是曲折的，耗费个人精力，也占用他人资源，不好不好，一点都不佛系。

究其原因，还是对为什么出现各种冲突不明了，查了些资料，稍微实践和思考了下，有点理解分享出来。

---

### 冲突出现原因
总结起来一句话：在不同版本的同一个位置出现了不同的东西。

具体解释如下：
- 版本：每一次提交都会是当前项目的一个版本，会有一个编号；
- 不同版本：冲突一定是出现在不同版本间的，通常是出现在相隔两个版本号的两个版本之间；
- 同一个位置：这里指的是同一个文件路径，或者同一个文件路径下同一个文件中的同一行等；
- 不同的东西：这里说的是不同的代码，或者不同的路径，或者不同的文件；

举个栗子：
- 程序员A在版本1001编写了文件a和文件b，文件a中第5行代码为

```
print("shit happens")
```

- 程序员BUpdate代码后，在1001版本上编写了文件a，把第5行代码改写为
```
print("who says")
```
- 并上传代码，生成版本1002；
- 程序员A发觉代码不对（拖出去祭天），修改a文件第5行代码为
```
print("no shit happens")
```
- 并提交代码，svn提示 out of date
- 更新代码，提示冲突

不想解释了，自己对照着看吧。

同理，文件的冲突也是这样，有人删除了文件，甚至文件夹等

这篇博客：[svn conflict 冲突解决](https://www.cnblogs.com/aaronLinux/p/5521844.html#T1) 用截图展示了过程，可以看下。

---

### 解决冲突
总结起来一句话：根据改动内容以及项目情况决定。

具体而言，这是一句废话，但又是最容易忘记的真理。

举个栗子
- A改了文件b，文件a，删除了文件c，提交了代码，版本号1003
- B更新代码，改动了文件b，改动了文件d，提交代码，版本号1004
- 经过了多次变动...
- A修改了文件b，提交代码，有冲突，冲突情况为
1. 文件b代码冲突；
2. 文件d代码冲突
3. 文件a tree conflict
3. 版本号1100
3. 其他

分析下
1. 文件b大家都动了，这个时候要特别小心的看别人的代码是干嘛的，再根据自己代码的作用进行处理；常规动作是，都保留，排排顺序；
2. 文件d，程序A从来没有操作过，应该是不应该出现代码冲突的，以1009版本代码为准即可；（当然还是要具体看看哪里出现冲突的，应该是之前代码不规范书写导致）
3. 文件a出现tree conflict，也就是说a应该是被删除了，咨询之前的开发人员，为啥删了我的a~~~然后删除他；
4. 注意自己的版本号什么的，信息和数据是王道

另外，比如后端程序员合分支遇到前端代码冲突的，请尽量不要乱动好么~~

---

#### 以上，以及以下
以上就是我大概踩过的坑了

另外，这两天还有一些学习记录下

#### StringUtils.defaultString
org.apache.commons.lang3.StringUtils.defaultString

```
String a = "ddd";
String v = StringUtils.defaultString(a,null);
//等价于
String vv = null == a ? null : a;
```
看似废物，但是，若是

```
StringUtils.defaultString(classA.getData().getIou(),null);
null == classA.getData().getIou() ? null : classA.getData().getIou();
```
哪一个更清爽？

#### Byte
前两天，被一个Byte的比较给搞晕了...郁闷

其实就是一篇博客的问题

> Java 有8中基本数据类型，分别是byte、int、long、char、float、double、boolean。
> 1.byte、char的简单介绍
> 有时候总是搞不清byte、char,所以就现在好好的整理一下:
> 
> 一个byte型整数在内存中占8位，也就是一个字节. 表数范围:-128 --127 . （字符类型char 2个字节）。
> 
> Java 中整形常量有三种表示方式：
> 
> 其中8进制的整数常量以0开头. 16进制的整数以0x或0X开头,10-15分别a-f开头（此处的a--f不区分大小写）来表示.
> 
>  
> 
>  
> 
> 字符型
> 
> 字符型通常用于表示单个字节,字符常量必须使用单引号('')括起来.JAVA语言使用16位的Unicode编码集作为编码方式,它支持各种语言的字符.
> 
> 字符常量有三种表示形式:
> 
> 直接通过单个字符来指定字符常量:例如，'A'、'a'、'8'等。
> 通过转义字符表示特殊的字符常量.例如：'\n'、'\t'等。
> 直接适用Unicode值来表示字符常量.格式是:'\uXXXX',其中XXXX代表一个16的整数.
> 字符型常量也可以用16进制编码方式表示，范围是:'\u0000'----'\uFFFF'，一共可以表示65536个字符,其中前256个字符('\u0000'---'\u00FF')和ASCII码中的字符完全重合.
> 
> 不仅如此，字符型的值也可以作为整数型来使用,但它是无符号整数，即全部是整数，但他是一个16位的无符号整数,表数范围:0----65535.
> 
> 如果把一个0---65535内的int型整数赋给char类型的变量，系统会自动把这个int型整数当成char类型来处理。
> 
> 下面是一个简单的例子:
> 
> 
> ```
> public class TestChar {  
>   
>     public static void main(String[] args) {  
>         //直接指定单个字符作为字符常量  
>         char aChar='a';  
>         //使用转义字符作为字符常量.  
>         char enterChar='\r';  
>         //使用Unicode编码值来作为字符常量  
>         char ch='\u9999';  
>         System.out.println(ch);  
>         //定义一个中字符常量  
>         char zhong='中';  
>         //直接将一个char变量当成int变量类型使用  
>         int zhongValue=zhong;  
>         System.out.println(zhongValue);  
>         //直接将一个0--65535的int型整数赋值给一个char变量  
>         char c=97;  
>         System.out.println(c);  
>     }  
> }
> ```
> 
>  
> 
> 2.转义字符
> 
> Java中的单引号、双引号和反斜线都有特殊的用途，应该使用转义字符的表示形式.
> 
> 例如我们想在JAVA程序中表示一个绝对路径："c:\codes，但这种写法得不到我们希后一个反斜线组成望的结果，因为JAVA会把反斜线当成转义字符,所以我们应该写成如下形式:"c:\\codes"，只有同时写两个反斜线，JAVA会把第一个反斜线当成转义字符，和后一个反斜线组成真正的斜线.
> 
> JAVA中 常用的转义字符：
> 
> \b        退格符
> 
> \n        换行符
> 
> \r        回车符
> 
> \t        制表符
> 
> \"        双引号
> 
> \'        单引号
> 
> \\        反斜线
> 
>  
> 
> 3.强制类型转换
> 下面程序示范了强制类型转换的实例
> 
> 
> ```
> public class NarrowConversion {  
>     public static void main(String[] args) {  
>         int iValue=233;  
>         //强制把一个int类型的值转换成byte类型的值  
>            byte bValue=(byte) iValue;  
>            //将输出-23  
>            System.out.println(bValue);  
>              
>              double dValue=3.98;  
>            //强制把一个double型的值转换为int  
>              int i=(int) dValue;  
>              System.out.println(i);  
>     }  
> }
> ```
> 
> 运行后的输出结果:
> 
> 
> ```
> -23  
> 3
> ```
> 
>  上面程序中，将一个233整型强制类型转换为byte类型，从而变成了-23，这就是典型的溢出。
> 
> 现在就上面的结果进行一下分析：
> 
> 首先我们来了解一知识下计算机的基础：
> 
> 所有数字在计算机底层都是以2进制形式存在的 ,原码就是直接将一个10进制数转换成2进制数，但计算机是以补码的形式保存所有的整数。补码的计算规则如下：正数的补码和原码完全相同，负数的补码是其反码加1；反码是对原码按位取反,除了最高位(符号位)保持不变。
> 
> 现在继续，看看上面的转换问题，下面示范了转换的过程：
> 
> 
> ```
> //32位的int型 00000000000000000000000011101001  
> //转换为8位的byte型11101001
> ```
> 
> 
>  上图，就是负数补码和源码转换的示意图！
> 
>  
> 
>  下面的程序时随机生成一个6位的字符串！
> 
> ```
> public class RandomStr {  
>   
>     public static void main(String[] args) {  
>           
>         //定义一个空的字符串  
>         String result="";  
>         //进行6次循环  
>         for(int i=0;i<6;i++){  
>             //生成一个97--122的int型整数  
>              int intVal=(int) (Math.random() *26+97);  
>              //将int 强制转换为char后连接到result后面  
>              result=result+(char)intVal;  
>         }  
>         //随机输出字符串  
>         System.out.println(result);  
>         System.out.println((char)97);  
>         System.out.println((char)122);  
>     }  
> }
> ```
> 

对于我而言，其实就是记得Byte是小型的int就好了..基本类型哦~

#### java中计时

```
long begintime = System.currentTimeMillis();
long endtinme=System.currentTimeMillis();
long costTime = (endtime - begintime);
```
当然，还有啥[更精确的计时](https://www.cnblogs.com/lt1726/p/5896132.html)

也就是说第一种方法计算毫秒级的，这个能精确到纳秒级，对于我的level来说，脱裤子放屁诶~

#### tomcat中war和war explode部署方式

> war模式：将WEB工程以包的形式上传到服务器 ；
>
> war exploded模式：将WEB工程以当前文件夹的位置关系上传到服务器；

[Tomcat部署时war和war exploded区别以及平时踩得坑](http://blog.csdn.net/xlgen157387/article/details/56498938)这篇讲的还是挺准确的

所以，我会用tomcat发布自己的东西哈？