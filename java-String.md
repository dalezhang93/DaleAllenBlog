

<!-- toc orderedList:0 depthFrom:1 depthTo:6 -->

* [创建字符串](#创建字符串)
  * [常量池](#常量池)
  * [==,equals()和 hashCode()](#equals和-hashcode)
* [StringBuffer 和 StringBuilder](#stringbuffer-和-stringbuilder)
  * [字符串拼接](#字符串拼接)
  * [字符串缓冲区容量](#字符串缓冲区容量)
* [String作为参数传递](#string作为参数传递)

<!-- tocstop -->


`String`不是基础数据类型,是对象,但又是一个特殊的对象.

### 创建字符串

第一种方式`String str1 = "string";`
第二种方式`String Str2 = new String();`

第一种方式使用字面量,必须显式初始化,其值编译时就确定
第二种方式使用`new`创建字符串,无参情况下值为空字符串,是在运行时创建的对象,源码如下:
```
public String() {this.value = new char[0];}
```

#### 常量池
这里的常量池指的是全局字符串池（string pool）
使用字面量创建字符串,首先在常量池中查看是否有相同的字面量,如果有直接返回该引用,没有就再创建一个.
而使用`new`关键字创建字符串,每次都会在堆内存中创建新的对象
`intern()`方法可以将堆内存中字符串,在常量池中也创建一个(jdk6),或者直接复制堆内存中的引用(jdk7),返回常量池中该字面量的引用.
```
String str1 = "abc";
String str2 = new String("abc");
str2 = str2.intern();
System.out.println(str1==str2); // jdk6:false,jdk7:true
```

#### ==,equals()和 hashCode()

- == 比较对象的内存地址是否相同,使用字面量创建的字符串和使用`new`关键字创建的字符串并不==
- equals()用来比较两个对象是否"相等",具体相等条件要自己实现,遵循以下原则
  - 自反性:x.equals(x)返回 true
  - 一致性:如果两个对象==,也就是内存地址相同,返回 true
  - 对称性:x.equals(y)为 true,则y.equals(x)为 true
  - 传递性:x.equals(y),y.equals(z)均为 true,则x.equals(z)也为 true
  - 非空性: x.equals(null)返回 false
String对象 Jdk 已经实现了该 equals()方法.源码如下:
```
public boolean equals(Object anObject) {
        if (this == anObject) {
            return true;// 只要==,equals()一定返回 true
        }
        if (anObject instanceof String) {
            String anotherString = (String) anObject;
            int n = value.length;
            if (n == anotherString.value.length) {
                char v1[] = value;
                char v2[] = anotherString.value;
                int i = 0;
                while (n-- != 0) { // 创建char数组,循环比较每一位
                    if (v1[i] != v2[i])
                            return false;
                    i++;
                }
                return true;
            }
        }
        return false;// 传入参数不是 String对象,则返回false
    }
```
- hashCode()方法被用来获取给定对象的唯一整数,默认的，Object类的hashCode()方法返回这个对象存储的内存地址的编号.String对象重写了该方法,根据字符串每一位计算,只要字符串字面量相同 hashCode返回值就一样,源码如下:
```
public int hashCode() {
        int h = hash;
        if (h == 0 && value.length > 0) {
            char val[] = value;

            for (int i = 0; i < value.length; i++) {
                h = 31 * h + val[i]; // s[0]*31^(n-1) + s[1]*31^(n-2) + ... + s[n-1]
            }
            hash = h;
        }
        return h;
    }
```
### StringBuffer 和 StringBuilder

String,StringBuffer和StringBuilder类都是final不可变,不同的是StringBuffer 和 StringBuilder 类的对象能够被多次的修改，并且不产生新的未使用对象,而String 类的对象每次修改都会创建一个新的对象.

StringBuilder相对于StringBuffer没有同步的操作,不是线程安全的,但也因此在速度上比StringBuffer快.

#### 字符串拼接
- String对象的+操作,在底层会创建StringBuilder对象,使用append 方法拼接,并使用toString()转换为 String.
- 在不要求线程安全的情况下,使用 StringBuilder 速度要比 StringBuffer 快,+操作最慢
String 对象也可以使用 concat()方法拼接字符串,与+操作不同的时,参数只能是 String类型,而 +操作可以将其他类型转换为 String 类型
- 但是如果是`String str = "abc"+"ef"`,str 变量在编译时就已确定是"abcef",因此在这种情况下速度比StringBuilder更快

#### 字符串缓冲区容量
- 使用 StringBuffer、StringBuilder 的无参构造器产生的对象默认拥有 16 个字符长度大小的字符串缓冲区
- 如果参数为 String 的构造器,默认的字符串缓冲区容量是 String 对象的长度 + 16 个长度的大小
- 如果缓冲区容量不够时,会自动调用方法扩张缓冲区容量,因此如果字符串比较长,可以预先分配足够多的缓冲区容量
- capacity()方法显示字符串缓冲区容量大小
- 以 StringBuffer 为例,指定缓冲区大小为20的的 StringBuffer 对象`StringBuffer sb = new StringBuffer(20)`

### String作为参数传递

> java所有的参数传递都是值传递,传递的是变量所代表的值的副本

```
@Test
public static void main(String[] args){
        int i = 1;
        String str = "123";
        StringBuffer sb = new StringBuffer("abc");
        change(str,sb,i);

        System.out.println(str);// "123"(未改变)
        System.out.println(sb);// "abcdef"(改变)
        System.out.println(i);// 1(未改变)

    }
    public static void change(String str,StringBuffer sb,int i){
        i = 2;
        str = "234";
        sb = sb.append("def");
    }
```

- 对于 String 实例 str,在栈内存中存在,传递给 change()方法时,在栈内存中创建另一个副本str',指向的是同一个对象,当在change()方法中改变String对象时,又创建了一个 Strring 对象,并且 str' 指向新创建的对象,原来的 str 仍指向原来的对象,当方法结束后, 副本str'销毁,副本创建的对象在合适的时候被回收,因此原来的对象并没有改变
- 对于 StringBuffer 实例 sb,同样传递一个副本 sb' 发给 change()方法,不同的是,在方法中改变时,并没有创建新的对象,直接对对象进行操作,方法结束后虽然副本被销毁,指向的对象已经改变.
