
[TOC]

# Scala安装

## 配置环境变量

### SCALA_HOME

```
C:\software\scala2.11
```

### Path

```
;%SCALA_HOME%\bin
```

## 验证是否配置成功

在cmd命令中输入 **scala -version** 命令，如果正确显示版本号，则说明配置成功。

# scala的变量和常量

## var、val

* 变量：使用var来声明；
* 常量：使用val来声明，常量不能改变；

```

object Main {
  def main(args: Array[String]): Unit = {
       //跟Java不同的是，所有的scala类型都是类
       val num = 100;    //这种声明方法，省略了数据类型
       var str : String = "Hello World";
       println(num);
       println(str);
  }
}

```

## 声明多个变量/常量

``` 
//args是程序接收的参数

//如果方法返回值是数组
val Array(logInputPath, resultOutputPath) = args

//上面的声明方式等价于
val logInputPath = args(0)
val resultOutputPath = args(1)

```

# Scala基础知识

## String和Int之间的类型转换

```
object Main {
  def main(args: Array[String]): Unit = {
    //String转换为Int
    println("1000".toInt);
    //Int转换为String
    println(233.toString);
  }
}

```

## Any类

scala中Any类是所有类的超类。

## Unit类

scala中使用Unit类来表示一个空值，相当于java中的void。

## 输入输出

```
object Main {
  def main(args: Array[String]): Unit = {
    var password = readLine("请输入密码：")
    println("您输入的密码是" + password)
  }
}

```

## 条件表达式

在scala中，表达式会将最后一条语句的结果作为返回值；如下：

```
object Main {
  def main(args: Array[String]): Unit = {
    val x = 100;
    val b = if( x > 0) 1 else -1
    println(b)
  }
}

```

## 循环

### while

使用while循环打印9*9乘法表

```
object Main {
  def main(args: Array[String]): Unit = {
    var row = 1
    while(row <= 9){
      var col = 1
      while(col <= row){
        printf("%d * %d = %d\t", row, col, row * col)
        col += 1
      }
      println()
      row +=1
    }
  }
}

```

### for

```
object Main {
  def main(args: Array[String]): Unit = {
    //打印1..10, to是一个闭合区间
    for(x <- 1 to 10) println(x)

    //打印1..9, until是一个半开半闭区间
    for(x <- 1 until 10) println(x)
  }
}

```

scala没有break、continue语句，可以使用Breaks对象的break()方法来代替

```
import scala.util.control.Breaks

object Main {
  def main(args: Array[String]): Unit = {
    for(x <- 1 to 10){
      if( x > 5) Breaks.break();
      println(x)
    }
  }
}

```

### for高级

```
object Main {
  def main(args: Array[String]): Unit = {
    //for双重循环, if i != j 表示守卫条件，非必须
    for(i <- 1 to 3; j <- 1 to 4 if i != j ){
      printf("%d * %d = %d", i, j, i*j)
      println()
    }
  }
}

```

scala中还可以使用for、yield循环返回一个集合；如果for循环体以yield开始，则该循环会构造出一个集合，每次迭代生成集合中的一个值。

```
object Main {
  def main(args: Array[String]): Unit = {
    var res = for(x <- 1 to 10) yield x % 3
    println(res)
  }
}

```

## 操作符重载

scala中可以使用任何的字符来命名方法和类。

```
object Main {
  def main(args: Array[String]): Unit = {
    println(1 + 2);      //这里的 + 其实是一个方法，也可以使用下面的方法来调用
    println(1.+(2));

    //scala 没有提供 ++ -- 操作，我们可以使用 += 1、 -= 1 来代替
    var num = 23;
    num += 1
    println(num);
    num -= 1
    println(num);
  }
}

```

## 函数和方法的区别

* 函数：不依赖于任何对象；

* 方法：必须依赖对象调用；

  ```
  import scala.math._
  object Main {
    def main(args: Array[String]): Unit = {
      //scala中的函数可以直接调用，不必依赖于对象
      println(min(1,2));
    }
  }
  
  ```

## lazy

lazy延迟计算：lazy 关键字修饰的变量在初始化的时候并不会加载，只有在第一次使用的时候才会加载资源。

```

object Main {
  def main(args: Array[String]): Unit = {
    //虽然文件的路径为空，但是这一行并不会报错，因为使用了lazy
    lazy val x = scala.io.Source.fromFile("").mkString
    //这里抛出 java.io.FileNotFoundException 异常
    println(x);
  }

}

```

## 异常处理

Scala异常的工作机制和Java一样，不过，与java不同的是，scala没有“受检”异常—你不需要必须处理或者抛出某些异常。

```
import java.io.IOException

object Main {
  def main(args: Array[String]): Unit = {
    try{
      "hello".toInt
    }catch {
      // 并不需要特别关注变量名的时候，可以使用 _
      case _: Exception => println(" This is Exception ")
      case ex: IOException => ex.printStackTrace()
    }finally {
      println(" This is finally ")
    }
  }
}

```

## 数组

### 定长数组

如果你需要一个长度不变的数组，可以使用scala中的Array。

```

object Main {
  def main(args: Array[String]): Unit = {
    //创建一个长度为10的数组，所有元素初始化为0
    val nums = new Array[Int](10)
    println(nums(3))

    //创建一个长度为2的数组，第一个元素为 "hello" , 第二个元素为 "World"
    val s = Array("Hello","World")
    println(s(1))

  }
}

```

### 可变数组

对于那种长度需要变化的数组，Java中有ArrayList, Scala中的等效的数据结构为ArrayBuffer。

```
import scala.collection.mutable.ArrayBuffer

object Main {
  def main(args: Array[String]): Unit = {

    val b = ArrayBuffer[Int]()

    //在尾端添加数据
    b += (1,2,7,4)
    //在尾端以数组的形式添加数据
    b ++= Array(101,102)
    println(b)

    //在下标2之前插入一个元素
    b.insert(2,200)
    println(b)

    //移除下标为2的元素
    b.remove(2)
    println(b)
  }
}

```

### 遍历数组

使用for循环来遍历数组

```

object Main {
  def main(args: Array[String]): Unit = {

    var nums = Array(1,3,5,8,12)
    for(num <- 0 until nums.length){
      println(nums(num))
    }

  }
}

```

### 数组转换

将一组数据通过运算转换为另外一组数据。

```

object Main {
  def main(args: Array[String]): Unit = {

    var nums = Array(1,3,5,8,12)
    //请留意结果是个新集合——原始集合并没有受到影响
    //过滤集合中的偶数，并将说有偶数乘以2
    var res = nums.filter(_ % 2 == 0).map(2 * _)
    res.foreach(println)
  }
}

```

### 数组的最大值、最小值、排序

```
import scala.collection.mutable.ArrayBuffer

object Main {
  def main(args: Array[String]): Unit = {

    var nums = Array(12,5,3,8,1)
    //计算数组的最大值
    println(nums.max)
    //计算数组的最小值
    println(nums.min)

    //排序,改变数组本身
    scala.util.Sorting.quickSort(nums)
    println(nums.mkString("(",",",")"))

  }
}

```

### 二维数组

```

object Main {
  def main(args: Array[String]): Unit = {

    //定义一个3行4列的2维数组
    val arr = Array.ofDim[Int](3,4)

    //给数组赋值
    arr(0)(1) = 1
    arr(1)(1) = 11
    arr(2)(2) = 22

    //遍历数组
    for(i <- 0 until arr.length; j <- 0 until arr(i).length){
      println("[" + i + ", "+ j + "] = " + arr(i)(j))
    }

  }
}

```

### 与java交互

使用Scala 的 JavaConversions方法对象进行转换（隐式转换）。

将Java中的ArrayList转换为Scala中的ArrayBuffer，如下：

```
import java.util
import scala.collection.JavaConversions._

object Main {
  def main(args: Array[String]): Unit = {
      var list = nums;
      //list是一个Java集合，不能调用我在Scala中最喜爱的foreach方法，因为它不存在
      //通过导入JavaConversions对象的方法，将list隐式转化为scala对象
      list.foreach(println)
  }

  def nums = {
    var list = new util.ArrayList[Int]()
    list.add(1)
    list.add(2)
    list
  }
}

```

## Map

### 不可变Map

```

object Main {
  def main(args: Array[String]): Unit = {
    val map = Map(100 -> "tom", 200 -> "tomas", 300 -> "tomasLee")
    println(map(300))
  }
}

```



### 可变Map

```

object Main {
  def main(args: Array[String]): Unit = {
    //创建可变的Map
    val map2 = scala.collection.mutable.Map(100 -> "tom", 200 -> "tomas", 300 -> "tomasLee")

    //追加Map值
    map2 += (400 -> "Steven", 500 -> "Daived")
    println(map2(500))

    //移除Map值
    map2 -= 100

    //迭代Map
    for((k,v) <- map2){
         println(k + ": " + v)
    }

  }
}

```

### 反转键、值

scala中可以将 key value对调，然后将结果赋值给一个新的变量。

```

object Main {
  def main(args: Array[String]): Unit = {
    val map = scala.collection.mutable.Map(100 -> "tom", 200 -> "tomas", 300 -> "tomasLee")
    //调换key value
    val reversedMap = for((k,v) <- map) yield (v,k)
    //迭代Map
    for((k,v) <- reversedMap){
         println(k + ": " + v)
    }

  }
}

```

## 元组

元组是不同类型的值得聚集；在scala中最多为 22 元组。

```

object Main {
  def main(args: Array[String]): Unit = {
    //定义一个三元元组，对应的类为Tuple3
    val t = (1,12,"tom")
    println(t)

    //访问元组的第二个元素
    println(t._2) //方式1
    println(t _3) //方式2

    //将元组的第一个元素赋值给变量 a
    //将元组的第二个元素赋值给变量 b
    //将元组的第三个元素赋值给变量 c
    println("*******************************************")
    val (a,b,c) = t
    println(a)
    println(b)
    println(c)
  }
}

```

### zip

现在所有的省份构成一个不可变的数组，所有的省会构成一个不可变的数组；可以通过zip操作将省份和省会对应起来，如下：

```

object Main {
  def main(args: Array[String]): Unit = {
    //定义一个三元元组，对应的类为Tuple3
    val provice = Array("guang dong", "guang xi", "gan su")
    val city = Array("guang zhou", "nan ning", "lan zhou")

    val merge = provice.zip(city);
    println(merge.mkString)

  }
}

```

# 集合

## 不可变列表List

``` 
package com.dongk.scala

object ListDemo {

  def main(args : Array[String]):Unit = {

    //创建一个List
    var list1 = List(2,5,1,4)
    println("list1:" + list1)

    //list1.head的值是2
    var head = list1.head
    println("head:" + head)

    //list1.tail的值是List(5,1,4)
    var tail = list1.tail
    println("tail:" + tail)

    // :: 操作符从给定的头和尾创建一个新的列表
    // 注意 :: 是右结合的
    var list2 = 9 :: List(8,7)
    println("list2:" + list2)

    //Nil表示一个空集合
    var list3 = 1 :: 3 :: 5 :: Nil;
    println("list2:" + list3)

    var sumlist3 = sum(list3);
    println("sum : = " + sumlist3)
  }

  //使用模式匹配、递归遍历List求和
  def sum(list : List[Int]) : Int = list match {
    case Nil => 0
    case h :: t => h + sum(t)
  }

}
```

## 可变列表LinkedList

可变列表LinkedList和不可变列表List相类似，只不过你可以对elem引用赋值修改其头部，对next引用赋值修改其尾部。
下面我们使用循环将所有非负值都改为0。

``` 
package com.dongk.scala

object LinkedListDemo {

  def main(args : Array[String]):Unit = {
    var list1 = scala.collection.mutable.LinkedList(1,-2,7,9)
    //在这里cur就相当于一个迭代器，但是实际上它是一个LinkedList
    var cur = list1
    //遍历list，并将所有的负值都修改为0
    while (cur != Nil){
      if(cur.elem < 0) cur.elem = 0
      cur = cur.next
    }
    println(list1)
  }

}
```

## 不可变Set

``` 
package com.dongk.scala

/***
  * 一般
  *   不带等号的操作符操作不可变集合。
  *   带等号的操作符操作可变集合
  */
/
object SetDemo {

  def main(args : Array[String]):Unit = {
      //创建一个Set
    val set1 = Set(3,5,2)

    //添加元素
    val set2 = set1 + (2,4,5)
    println(set2) //Set(3, 5, 2, 4)

    //移除元素
    val set3 = set2 - 3
    println(set3) //Set(5, 2, 4)
  }
}
```

## 可变LinkedHashSet


``` 
package com.dongk.scala

/***
  * 一般
  *   不带等号的操作符操作不可变集合。
  *   带等号的操作符操作可变集合
  */

object LinkedHashSetDemo {

  def main(args : Array[String]):Unit = {
    //创建可变集合
    val set1 = scala.collection.mutable.LinkedHashSet("one","tow","three")
    val set2 = scala.collection.mutable.LinkedHashSet("ten")
    //添加一个元素
    set1 += "four"
    //添加一个集合
    set1 ++= set2
    println(set1)
    println(set2)
  }
}
```

## 常用的方法

``` 
package com.dongk.scala

import scala.collection.mutable.ArrayBuffer

object CollGeneMethodDemo {

  def main(args: Array[String]): Unit = {
    val b = ArrayBuffer[Int](2,4,6,8,10)

    //返回第一个元素
    val strHead = b.head
    println(strHead) //2

    //一个元素与另外一个元素的对偶
    val b1 = ArrayBuffer(1,3,5);
    val b2 = ArrayBuffer(2,4);
    val b3 = b1.zip(b2)
    println(b3)   //ArrayBuffer((1,2), (3,4))

    //映射
    val b4 = ArrayBuffer("tom","bob","tommas")
    //将b4全部转换为大写
    val b5 = b4.map(_.toUpperCase())
    println(b4)  //ArrayBuffer(tom, bob, tommas)
    println(b5) //ArrayBuffer(TOM, BOB, TOMMAS)

  }
}

```

# Scala 小括号和花括号

# 偏函数

**什么是偏函数？**

只会作用于指定类型的参数或指定范围值的参数实施计算，超出它的界定范围之外的参数类型和值它会忽略。

下面就是一个偏函数的应用：

``` 
package com.dongk.scala.function

object FunctionPartially {

  def main(args: Array[String]): Unit = {
    val list1 = List(1,2,3)
    //在这里，case i:Int=>i+1构建的匿名函数等同于(i:Int)=>i+1
    val list2 = list1.map {case i:Int=>i+1}
    //List(2, 3, 4)
    println(list2)

    val list3 = List(1, 3, 5, "seven")
    // won't work
    /**
      * 传递给map的case语句构建的是一个普通的匿名函数，
      * 在把这个函数适用于”seven”元素时发生了类型匹配错误
      */
    //val list4 = list3.map { case i: Int => i + 1 }

    /**
      * 而对于collect,它声明接受的是一个偏函数：PartialFunction，
      * 传递的case语句能良好的工作说明这个case语句被编译器自动编译成了一个PartialFunction
      */
    //List(2, 4, 6)
    val list5 = list3.collect { case i: Int => i + 1 }
    println(list5)


  }

}

```

**正式认识偏函数Partial Function**

下面的例子只是作用于Int类型，我把这称之为偏向Int

``` 
package com.dongk.scala.function

object FunctionPartially01 {

  def main(args: Array[String]): Unit = {

    val inc = new PartialFunction[Any, Int] {
        def apply(any: Any) = any.asInstanceOf[Int]+1
       def isDefinedAt(any: Any) = if (any.isInstanceOf[Int]) true else false
      }

    val list = List(1, 3, 5, "seven")
	//List(2, 4, 6)
    val list2 = list collect inc

    println(list2)
  }

}

```

PartialFunction特质规定了两个要实现的方法：apply和isDefinedAt，isDefinedAt用来告知调用方这个偏函数接受参数的范围，可以是类型也可以是值，在我们这个例子中我们要求这个inc函数只处理Int型的数据。apply方法用来描述对已接受的值如何处理，在我们这个例子中，我们只是简单的把值+1，注意，非Int型的值已被isDefinedAt方法过滤掉了，所以不用担心类型转换的问题。

上面这个例子写起来真得非常笨拙，和前面的case语句方式比起来真是差太多了。这个例子从反面展示了：通过case语句组合去是实现一个偏函数是多么简洁。实际上case语句组合与偏函数的用意是高度贴合的，所以使用case语句组合是最简单明智的选择。

当然，如果偏函数的逻辑非常复杂，可能通过定义一个专门的类并继承PartialFunction是更好选择。

下面的例子只是作用于Int、String类型，我把这称之为偏向Int、String

``` 
package com.dongk.scala.function

object FunctionPartially01 {

  def main(args: Array[String]): Unit = {

    val inc = new PartialFunction[Any, Any] {
      def apply(any: Any): Any = {

        if(any.isInstanceOf[Int]) {
         return any.asInstanceOf[Int] + 1
        }

        if(any.isInstanceOf[String]) {
         return any.asInstanceOf[String] + "Hello"
        }

      }

      def isDefinedAt(any: Any) = {
        if (any.isInstanceOf[Int] || any.isInstanceOf[String])
          true
        else
          false
      }

    }

    val list = List(1, 3, 5, "seven")
	//List(2, 4, 6, sevenHello)
    val list2 = list collect inc

    println(list2)
  }

}
```

**为什么偏函数只能有一个参数？**

为什么只有针对单一参数的偏函数，而不是像Function特质那样，拥有多个版本的PartialFunction呢？在刚刚接触偏函数时，这也让我感到费解，但看透了偏函数的实质之后就会觉得很合理了。我们说所谓的偏函数本质上是由多个case语句组成的针对每一种可能的参数分别进行处理的一种“结构较为特殊”的函数，那特殊在什么地方呢？对，就是case语句，前面我们提到，case语句声明的变量就是偏函数的参数，既然case语句只能声明一个变量，那么偏函数受限于此，也只能有一个参数！说到底，类型PartialFunction无非是为由一组case语句描述的函数字面量提供一个类型描述而已，case语句只接受一个参数，则偏函数的类型声明自然就只有一个参数。

但是，这并不会对编程造成什么阻碍，如果你想给一个偏函数传递多个参数，完全可以把这些参数封装成一个Tuple传递过去！

# Scala 函数字面量

 (x:Int) => println(x)  就是一个简单的函数字面量。 
 
 => 左边是字面量的参数列表；
 =>右边是函数体； 
 
 如果函数体操作不止一行， 要用花括号括起来。如下：
 
``` 
 (x:Int) => { 
                        println(x)
						println(x + 1)
					  }
```

函数字面量使用最多的方式是作为参数传递。 下面定义一个这样一个类：

``` 
package com.dongk.scala.function

object FunctionLiterals {

  def main(args: Array[String]): Unit = {
    doSomething1()
  }

  def doSomething(func : Int => Unit){
    func(233)
  }

  def doSomething1(){
    doSomething( ( (x : Int) => println(x) ) )
  }

}

```


# Scala比较器Ordered与Ordering

在项目中，我们常常会遇到排序（或比较）需求，比如：对一个Person类

``` 
package com.dongk.scala.order

case class Person(name: String, age: Int) {
  override def toString = {
    "name: " + name + ", age: " + age
  }
}

```

按name值逆词典序、age值升序做排序；在Scala中应如何实现呢？

## 两个特质

Scala提供两个特质（trait）Ordered与Ordering用于比较。其中，Ordered混入（mix）Java的Comparable接口，而Ordering则混入Comparator接口。众所周知，在Java中

* 实现Comparable接口的类，其对象具有了可比较性；
* 实现comparator接口的类，则提供一个外部比较器，用于比较两个对象。


Ordered与Ordering的区别与之相类似：

* Ordered特质定义了相同类型间的比较方式，但这种内部比较方式是单一的；
* Ordering则是提供比较器模板，可以自定义多种比较方式。

## Ordered

Ordered特质更像是rich版的Comparable接口，除了compare方法外，更丰富了比较操作（<, >, <=, >=）：

``` 
package com.dongk.scala.order

object OrderedTest {

  def main(args: Array[String]): Unit = {
    val t1 = new TeacherOrdered("丹尼斯·里奇", 70)
    val t2 = new TeacherOrdered("Linus Benedict Torvalds", 49)
    val bigger = new ComparableGeneralObject(t1,t2).bigger
    print(bigger)
  }

}

/**
  * 比较任意两个对象，并返回较大的那一个
  * */
class ComparableGeneralObject[T <: Ordered[T]](a:T, b:T){
  def bigger = {
    if(a > b) a
    else b
  }
}

/**
  *
  * TeacherOrdered 类，表示一个可以进行比较的实体类
  * */
class TeacherOrdered(val name:String, val age:Int) extends  Ordered[TeacherOrdered]{
  override def compare(that: TeacherOrdered): Int = {
    this.age - that.age
  }

  override def toString: String = {
    this.name + "\t" + this.age
  }
}


```

## Ordering

Ordering一般放在类似于Sorting、top这类的排序方法中用来设定排序顺序。

``` 
package com.dongk.scala.order

import scala.util.Sorting

object OrderingTest {

  def main(args: Array[String]): Unit = {
    val pairs = Array(("a", 5, 2), ("c", 3, 1), ("b", 1, 3))
    //意思是以Array里每一个元组的第三个元素为排序依据（元组下标从1开始），_._2就是取第二个元素
    Sorting.quickSort(pairs)(Ordering.by[(String, Int, Int), Int](_._2))
    pairs.foreach(println)
  }
}

```

# Scala面向对象

## 函数

### 定义函数

下面定义一个add函数

```

object Main {
  def main(args: Array[String]): Unit = {
    println(add(11,22))
  }
  
  def add(x:Int, y:Int): Int = {
       x + y
  }
}

```

### 函数的默认值、命名参数

scala中函数中的参数可以指定默认值，在调用函数的时后可以通过参数名称指定传递的参数。

```

object Main {
  def main(args: Array[String]): Unit = {
    //根据参数名称，指定传递第二个参数
    println(decorate(str = "Hello Word"))
  }

  //此函数的功能是在字符串的头和尾添加特殊的修饰符
  //前缀修饰符默认为 "[["
  //后缀修饰符默认为 "]]"
  def decorate(prefix:String = "[[", str:String, suffix:String = "]]"): String = {
    prefix + str + suffix
  }
}

```

### 变长参数

在scala中可以通过下面的方法，定义一个参数可变的函数。

```

object Main {
  def main(args: Array[String]): Unit = {
    //_* 表示将一个集合当做序列来处理
    println(sum((1 to 10): _*))
  }

  /*
  *
  * 1、 * 表示此参数为可变长度参数
  * 2、 args.head 表示可变参数中的第一个参数
  * 3、 args.tail 表示可变参数中除第一个参数之外的所有参数的集合
  * 4、 _* 表示将一个集合当做序列来处理
  **/
  def sum(args: Int*): Int = {
     if(args.length == 0){
       0
     }else{
       args.head + sum(args.tail: _*)
     }
  }
}

```

### 过程

如果函数体包含在花括号中但没有前面的 = 号，那么返回类型就是Unit。这样的函数被称作过程。过程不返回值，我们调用它仅仅是为了它的副作用。

```

object Main {
  def main(args: Array[String]): Unit = {
    out("Hello World")
  }

  def out(str: String){
    println(str)
  }
}

```

### 函数柯里化

将原来接受两个参数的函数变成新的接受一个参数的函数的过程。新的函数返回一个以原有第二个参数为参数的函数。

``` 

package com.dongk.scala.function

object CurringDemo {
  def main(args: Array[String]): Unit = {
    println(sum1(3,2));
    println(sum2(3)(2));
  }

  //定义一个有两个参数的求和函数sum1
  def sum1(x:Int, y:Int) : Int = {
    return x + y;
  }

  //sum2函数是sum1函数的柯里化
  //等价于def sum2(x:Int)=(y:Int) => x+y
  def sum2(x:Int)(y:Int) : Int = {
    return x + y;
  }

}

```

使用柯里化特性可以将复杂逻辑简单化，并能将很多常漏掉的主函数业务逻辑之外的处理暴露在函数的定义阶段，提高代码的健壮性，使函数功能更加细腻化和流程化。

例如： 我们使用函数柯里化设计一个获取本地文本文件的所有行数据的功能。

``` 
package com.dongk.scala.curring

import java.io.{BufferedReader, File, FileReader}

object ReadFileLines {

  def main(args: Array[String]): Unit = {
    var lines : List[String] = getLines("c://test.txt")
    for(line <- lines)println(line)
  }

  def getLines(filename:String):List[String] = {
    getLinesInformation(filename)(isReadable)(closeStream)
  }

  def getLinesInformation(filename:String)(isFileReadable:(java.io.File) => Boolean)
              (closeableStream:(java.io.Closeable) => Unit) : List[String] = {
    val file = new File(filename)
    if(isFileReadable(file)){
      val readerStream = new FileReader(file)
      val buffer = new BufferedReader(readerStream)
      try{
        var list:List[String] = List()
        var str = ""
        var isReadOver = false
        while(!isReadOver){
          str = buffer.readLine()
          if(str == null)
            isReadOver = true
          else
            list = str :: list
        }
        list.reverse
      }finally {
        closeableStream(buffer)
        closeableStream(readerStream)
      }
    }else{
      List()
    }
  }

  def isReadable(file : java.io.File) = {
    if (null != file && file.exists() && file.canRead)
      true
    else
      false
  }

  def closeStream(stream : java.io.Closeable): Unit ={
    if(null != stream){
      try{
        stream.close()
      }catch {
        case ex : Exception => ex.printStackTrace()
      }
    }
  }
}

```

## 类

### 定义一个简单的类

```

class Count{
  //必须初始化字段
  private var value = 0;

  //方法默认是公有的
  def increment(): Unit ={
    value += 1
  }

  def current() = value
}
object Main {
  def main(args: Array[String]): Unit = {
     val cou = new Count
     cou.increment()
     println(cou.current())
  }
}

```

### 构造函数

* 主构造函数：主构造的参数直接放置在类名之后。
* 辅助构造函数：辅助构造函数的函数名必须是this，每一个辅助构造函数必须以一个其它的辅助构造函数或主构造函数的调用开始。

```

//id  使用val修饰，表示 id 是一个成员变量，且只会生成getter方法，不会生成setter方法
//name 使用var修饰，表示 name 是一个成员变量，且会生成getter、setter方法
//age 无任何修饰,表示 age 是一个成员变量, 只在主构造函数中使用
class Person(val id: Int, var name: String, age: Int){

    //辅助构造函数 1
  def this(id: Int){
    //调用主构造函数
    this(id,"Tom",20)
  }

    //辅助构造函数 2
  def this(id: Int, name: String){
    //调用主构造函数
    this(id,name,20)
  }

  def out(): Unit = {
    printf("%s age is %d(%d)",name,age,id)
  }
}
object Main {
  def main(args: Array[String]): Unit = {
    //使用辅助构造函数 1
    var p1 = new Person(1)
    p1.out()
  }
}

```

### getter和setter方法

scala会自动为属性生成getter和setter方法，具体规则如下

```

class Person{
  //private 修饰，其对应的getter、setter方法也是 private
  private var id = 0;

  //生成私有属性name
  //生成共有getter方法 name()
  //生成共有setter方法 name_=(String str)
  var name = "tom"

  //val 修饰只是生成 getter 方法，不生成 setter 方法
  val age = 100;
}
object Main {
  def main(args: Array[String]): Unit = {
    var p = new Person
    //方式一：修改name属性
    p.name_=("Bob")
    println(p.name)

    //方式二：修改name属性,其本质也是编译器自动转换成方式一的形式
    p.name = "steven"
    println(p.name)
  }
}

```

### private[this]修饰符

private[this]字段让私有化更进一步，让字段对象私有化；这意味着只有包含该字段的对象可以访问。

```
class Count{
  private[this] var value = 0;
  def increment(): Unit ={
    value += 1
  }
  def isLess(count: Count): Boolean ={
    //这里会有编译错误，不能在其他对象中访问此字段
    this.value < count.value
  }
}
```

## 对象

scala中没有静态方法或者静态字段，你可以使用object这个语法结构来达到同样的目的。

对于任何你在java中会使用单例对象的地方，在scala中都可以使用对象来实现。

```
object Accounts{
  private var lastNumber = 0
  def newUniqueNumber() = {
    lastNumber += 1
    lastNumber
  }
}
object Main {
  def main(args: Array[String]): Unit = {
    //当你在程序中需要一个新的唯一的账号时，调用Accounts.newUniqueNumber()即可
    println(Accounts.newUniqueNumber())
  }
}

```

### 伴生对象

在Java中，你通常会用到既有实例方法又有静态方法的类，在scala中可以通过伴生对象达到同样的目的。

类和伴生对象必须存在于同一个源文件中；类和它的伴生对象可以相互访问私有特性。

```
class Accounts{
  val id = Accounts.newUniqueNumber()
  private var balance = 0.0
  def deposit(amount: Double){ balance += amount}
}
object Accounts{
  private var lastNumber = 0
  def newUniqueNumber() = {
    lastNumber += 1
    lastNumber
  }
}
object Main {
  def main(args: Array[String]): Unit = {
    var acc1 = new Accounts
    var acc2 = new Accounts
    println(acc1.id)
    println(acc2.id)
  }
}

```

### apply方法

可以利用Scala伴生类的apply方法，在Scala中实现工厂方法。例如：

``` 
package com.dongk.scala

object ApplyDemo {

  def main(args : Array[String]):Unit = {
    //等价于 Animal.apply("dog")
    val dog = Animal("dog")
    val cat = Animal("cat")
    dog.speak
    cat.speak
  }

}

//特质Animal
trait Animal{
  def speak
}

//特质Animal的伴生对象
object Animal{
  def apply(s:String): Animal = {
    if(s == "dog")
      new Dog
    else
      new Cat
  }
}

//Animal的子类Dog
private class Dog extends Animal{
  override def speak: Unit ={
    println("woof")
  }
}

//Animal的子类Cat
private class Cat extends  Animal{
  override def speak: Unit ={
    println("meow")
  }
}

```

### unapply反向抽取

unapply方法和apply方法刚好相反，它是接受一个对象，从对象中抽取相应的值。

unpaaly方法主要用于模式匹配。

``` 

```

## 特质

### 什么是特质？

特质相当于java中的接口，除非实现特质的是一个抽象类，否则必须实现特质所有的抽象方法。

```
abstract class Animal{
  def speak
}

//摇摆尾巴
trait WaggingTail{
  def startTail: Unit ={println("tail started")}
  def stopTail{println("tail stopped")}
}

//四条腿的动物
trait FourLeggedAnimal{
  def walk   //走
  def run    //跑
}

class Dog extends  Animal with WaggingTail with FourLeggedAnimal{
  def speak{println("Dog says 'woof'")}
  def walk{println("Dog is walking")}
  def run{println("Dog is running")}
}
```



### 抽象字段和实际字段

- 抽象字段 ：无初始值的字段
- 实际字段 ：有初始值的字段

在继承了特质的类中，需初始化抽象字段的值，除非它是抽象类；覆写var属性不需要override关键字，但当覆写val字段时，必须使用override关键字。

```
//披萨
trait PizzalTrait{
   val maxNumToppings: Int //最大的一个
}

class Pizzal extends PizzalTrait{
  override val maxNumToppings: Int = 10
}
```

# 泛型

## 类的泛型   

``` 
package com.dongk.scala.generic

//泛型类
class Pair[T,S](val first : T, val second : S){}

object GenericClass {
  def main(args: Array[String]): Unit = {
    //使用泛型类,指定泛型参数
    val pair1 = new Pair[String,Int]("tom",23)
    //使用泛型类，无需指定泛型参数（通过类型推断）
    val pair2 = new Pair("Bob",34)

    println(pair1)
    println(pair2)
  }
}

```

## 函数的泛型

``` 
package com.dongk.scala.generic

object GenericFunction {
  //泛型函数
  def getMiddle[T](a:Array[T]) = a(a.length / 2)
  def main(args: Array[String]): Unit = {
    val arr = Array("one","two","three","four","five")
    //使用泛型函数
    val middle = getMiddle(arr)
    println(middle)
  }
}

```

## 上界

S <: T
这是类型上界的定义，也就是S必须是类型T的子类（或本身，自己也可以认为是自己的子类)。

``` 
package com.dongk.scala.generic

//上界
//在这里我们为了保证第一个参数和第二个参数都有compareTo方法，我们给泛型加了上界限制
class PairUpper[T <: Comparable[T]](val first:T, val second:T){
  def upper = if(first.compareTo(second) > 0 ) first else second
}

object GenericUpper {
  def main(args: Array[String]): Unit = {
    val pair = new PairUpper[String]("aaa", "bbb")
    println(pair.upper)
  }
}

```

## 下界

U >: T
这是类型下界的定义，也就是U必须是类型T的父类(或本身，自己也可以认为是自己的父类)。

``` 
package com.dongk.scala.generic

/***
  * 假定我们有一个Pair3[Student3],我们应该允许第一个Pair3[Persion3]来替换，而
  * 不是允许其它无关的类去替换，我们可以通过下界限定来实现
  *
  */
class Person3
class Student3 extends Person3
class Pair3[T](val first:T, val second:T){
  //替换第一个元素，并返回一个新元素
  def replaceFirst[R >: T](newFirst : R) = new Pair3[R](newFirst,second)
}
object GenericLower {

  def main(args: Array[String]): Unit = {
    val student31 = new Student3
    val student32 = new Student3
    val person31 = new Person3
    val pair31 = new Pair3[Student3](student31,student32)
    val pair32 = pair31.replaceFirst(person31)
    println(pair32.first)
  }
}

```

# 隐式转换和隐式函数

隐式转换和隐式函数是scala中两个强大的工具。利用隐式转换和隐式函数可以提供优雅的类库，对类库的使用者隐藏掉那些枯燥乏味的细节。

## 隐式转换

我们需要某个类中的一个方法，但是这个类没有提供这样的一个方法，所以我们需要隐式转换，转换成提供了这个方法的类，然后再调用这个方法。

第一步，需要一个增强的类，里面提供我们想要的方法，接收的参数的类型一定要是被增强类的类型。
第二部，还需要在单例对象中写明隐式转换
第三步，把隐式转换函数导进来

在spark中隐式转换都写在伴生对象中，因为类的实例肯定能找到伴生对象的，在一个作用域当中。

``` 
package com.dongk.scala.implicitconvert

//这里的RichFile相当于File的增强类 需要将被增强的类作为参数传入构造器中
class RichFile(val file: java.io.File) {
  def read = {
    scala.io.Source.fromFile(file.getPath).mkString
  }
}

//implicit是隐式转换的关键字 这里定义一个隐式转换函数把当前类型转换成增强的类型
object Context {
  //File --> RichFile
  implicit def file2RichFile(file: java.io.File) = new RichFile(file)
}

object ImplicitConvert {

  def main(args: Array[String]): Unit = {
    //导入隐式转换
    import Context.file2RichFile
    //File类本身没有read方法 通过隐式转换完成
    //这里的read方法是RichFile类中的方法  需要通过隐式转换File --> RichFile
    println(new java.io.File("C:\\test.txt").read)
  }
}

```

## 隐式参数

``` 
package com.dongk.scala.implicitconvert

object ContextImplicits {
 //对于给定的数据类型，只能有一个隐式值，如果在这里存在两个String类型的隐式值，程序将会报错
 implicit val language: String = "Java"
}

object Param {
 //函数中用implicit关键字 定义隐式参数
 def print(context: String)(implicit language: String){
  println(language+":"+context)
 }
}

object ImplicitsParameters {
 def main(args : Array[String]) : Unit = {
   //隐式参数正常是可以传值的，和普通函数传值一样  但是也可以不传值，因为有缺省值(默认配置)
   Param.print("Spark")("Scala")   //Scala:Spark

   import ContextImplicits._
   //隐式参数没有传值，编译器会在全局范围内搜索 有没有implicit String类型的隐式值 并传入
   Param.print("Hadoop")          //Java:Hadoop
  }
}

```

## 利用隐式参数进行隐式转换

``` 
package com.dongk.scala.implicitconvert

object ImplicitConversionswithImplicitParameters {
  def main(args:Array[String]):Unit = {
    println(bigger(4, 3))                 //4
    println(bigger("Spark", "Hadoop"))    //Spark
  }

  /**
    * 1、Predef对象对大量已知类都提供了T => Ordered[T],所以才可以调用bigger(4, 3);
    * 2、Predef对象不需要导入；
    * */
  def bigger[T](a: T, b: T)(implicit ordered: T => Ordered[T]) = {
    if (ordered(a) > b) a else b
  }
}

```

# 模式匹配和样例类

模式匹配是检查某个值（value）是否匹配某一个模式的机制，一个成功的匹配同时会将匹配值解构为其组成部分。它是Java中的switch语句的升级版，同样可以用于替代一系列的 if/else 语句。

## 模式匹配

match表达式具有一个结果值。

``` 
package com.dongk.scala.matchcase

object MatchMain {
  def main(args: Array[String]): Unit = {
    System.out.println(matchTest(1))
    System.out.println(matchTest(11))
  }

  def matchTest(x: Int): String = x match {
    case 1 => "one"
    case 2 => "two"
    case _ => "many"
  }

}

```

## 案例类的模式匹配

案例类非常适合用于模式匹配。

``` 
package com.dongk.scala.matchcase

abstract class Notification
case class Email(sender: String, title: String, body: String) extends Notification
case class SMS(caller: String, message: String) extends Notification
case class VoiceRecording(contactName: String, link: String) extends Notification

object CaseMain {
  def main(args: Array[String]): Unit = {
    val someSms = SMS("12345", "Are you there?")
    val someVoiceRecording = VoiceRecording("Tom", "voicerecording.org/id/123")

    println(showNotification(someSms))
    println(showNotification(someVoiceRecording))
  }

  /**
    *  showNotification函数接受一个抽象类Notification对象作为输入参数，然后匹配其具体类型。
    * （也就是判断它是一个Email，SMS，还是VoiceRecording）。
    *  在case Email(email, title, _)中，对象的email和title属性在返回值中被使用，而body属性则被忽略，故使用_代替。
    **/
  def showNotification(notification: Notification): String = {
    notification match {
      case Email(email, title, _) =>
        s"You got an email from $email with title: $title"
      case SMS(number, message) =>
        s"You got an SMS from $number! Message: $message"
      case VoiceRecording(name, link) =>
        s"you received a Voice Recording from $name! Click the link to hear it: $link"
    }
  }

}

```

# 字符串插值器

Scala 提供了三种创新的字符串插值方法：s,f 和 raw.

## s 字符串插值器

在任何字符串前加上s，就可以直接在串中使用变量了。例子：

``` 
package com.dongk.scala.insertValue

object insertvalueofs {

  def main(args: Array[String]): Unit = {
    val name = "Bob"
    println(s"Hello $name") //Hello Bob
	//$表示这是一个对象
    //Hello insertvalueofs$ class
    println(s"Hello ${this.getClass.getSimpleName} class")
  }

}

```

## f 插值器

在任何字符串字面前加上 f，就可以生成简单的格式化串，功能相似于其他语言中的 printf 函数。当使用 f 插值器的时候，所有的变量引用都应当后跟一个printf-style格式的字符串，如%d。看下面这个例子：

``` 
package com.dongk.test

object ScalaMain001 {

  def main(args: Array[String]): Unit = {
    val height=1.9d
    val name="James"
    println(f"$name%s is $height%2.2f meters tall") //James is 1.90 meters tall

    // f 插值器是类型安全的。如果试图向只支持 int 的格式化串传入一个double 值，编译器则会报错。例如：

    println(f"$height%4d")  //这条语句程序会报错如下：

    //Error:(12, 16) type mismatch;
    //found   : Double
    //required: Int
    //println(f"$height%4d")
  }

}

```

## raw 插值器

了对字面值中的字符不做编码外，raw 插值器与 s 插值器在功能上是相同的。如下是个被处理过的字符串：

``` 
package com.dongk.test

object ScalaMain002 {

  def main(args: Array[String]): Unit = {
    //s 插值器用回车代替了\n,而raw插值器却不会如此处理。
    println(s"a\nb")

    //上面输出如下:
    //a
    //b

    //当不想输入\n被转换为回车的时候，raw 插值器是非常实用的。
    println(raw"a\nb")

    //上面输出如下:
    //a\nb

  }

}

```

# Scala 中一些常用的特质

## Product

在使用Spark做某些项目时，可能对某写数据进行分析时，会出现有很多字段，而Scala中，默认的最大的参数个数是22，因此如果当我们的字段太多时 ，22字段不能满足，我们可以同过实现Product这个特质，实现里面的方法，可以传更多的字段。

通过下标的方式访问（需要做和属性的映射关系）