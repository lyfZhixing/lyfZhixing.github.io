---
title: Java中的泛型
date: 2020-5-5 14:42:15
tags: [java, 泛型, 泛型擦除]
categories: [java基础]
---
# Java中的泛型
  Java是在JDK1.5之后引入泛型，为Java开发带来诸多好处，具体有两点：
  - 简单安全。加入泛型后在编译时有类型检查，提前发现问题，减少不必要的bug
  - 提升性能。加入泛型之前，需要强转以获得期望的值；使用泛型对容器操作则不行用进行类型转换，提到代码可读性和运行性能。

  从以下几个方面来了解泛型
  - [泛型分类](#type)   
  - [泛型的界限](#limit)  
  - [泛型擦除](#erase)
  - [使用](#use)   

<!-- more -->

## <span id = "type"> 泛型分类 </span>  
泛型有`泛型接口`,`泛型类`,`泛型方法`三种类型。  
- 泛型接口(/类)实例   
```
public interface List<E> extends Collection<E>

```

这种类型是比较常见的，如上面实例，java中很多常用的接口都使用了泛型作为规范。泛型参数在改类/接口被声明（类：实例化，接口：实现）时确定。  

> tips: 实例中的两个E的区别，List后的泛型E在接口List中可做泛型参数，Collection后的E是用来规范Collection的。

- 泛型方法  

```
    public <T, E extends Exception> T getSth(T t) throws E {
        // todo sth
        return t;
    }

    // in Class
    this.getSth(new Person("zhangsan", 20)); // 正常使用
    this.<Person, Exception>getSth(new Person("zhangsan", 20)); // 若没有处理异常则会编译错误
```

泛型方法是在方法的返回值之前加上`<T>`, `T`可以参数使用，其作用域过包括：返回值、方法参数、方法异常、方法主体  

方法本身没有形参也可以通过泛型来指定参数类型  

```

  public <T> T getSth2() {
       T t = null;
       // todo sth
       return t;
   }

   // in Class
   this.<String>getSth2();


```

## <span id = "limit"> 泛型的界限 </span>   

可以使用`?`、`extends`、`super`来确定泛型的边界。`?`代表某一个类型，`extends`确定上限，`super`确定下限。

- ?  

```

// 类：Person<T>  属性值：T t
    {
      Person<List> person1 = new Person<List>();

      Person<List> person2 = new Person<ArrayList>(); // 编译错误，与泛型类型不匹配
      Person<?> person3 = new Person<List>();

      person1.t = new ArrayList();// 不报错，向上转型

      person3.t = new ArrayList<>();// 编译报错， <?>无法确定具体是哪个类型
    }

```

- extends   

`<T extends E> ` 上界为E，即T必须为E或它的子类

> tips: `<? extends E>`即使加了上界，其读写权限与`<?>`是一样的   

- super   

`<T super E> ` 下界为E，即T必须为E或它的父类，其读写同样遵循`可向上转型，不可向下转型`


> tips:多个上界类型里最多只能有一个类，其他必须为接口，用&连接，若有类则类必须放在首位。例如`class Test<T extends E & Serializable & List> {}`。在类Test中，用到 T 时，反射会将其当做上界 E 来处理，但是在字节码中还是当做Object处理的。


## <span id = "erase"> 泛型擦除 </span>  

主流编程语言中，编译器处理泛型的方法：1）code specialization（代码特化）  2）code sharing（代码分享）

// todo

## <span id = "use"> 使用 </span>
