---
title: 注解和判断null
date: 2021-12-18T12:11:34.757Z
---
1. 注解

* @Override：子类继承父类，在继承的方法上
* @Deprecated： 过时，不推荐使用
* @SuppressWarnings：忽略编译器的警告

  以上三个作用在代码上
* 元注解

  * @Retention：标识存在哪里，源文件、类文件、运行时
  * @Documented：这些注解生成doc文件时显示
  * @Target：注解修饰的对象范围
  * @Inherited：标注的注解是InheritedAnno时， 当@InheritedAnno注解加在某个类A上时，假如类B继承了A，则B也会带上该注解。
  * @SafeVarargs：在使用变参方法时，调用时参数类型不同会警告，使用可以忽略
  * @FunctionalInterface：表示函数式接口
  * @Repeatable：表示同一个地方，可以加相同注解

2.  判断null

   ```java
   public class NullShow {
       // 只有小写
       Object object1 = null;
   //    Object object = Null;
       private static String number;

       public static void main(String[] args) {
           //两个null对比返回true
           String str = null;
           System.out.println(str==null);

           //null只能赋值引用类型，null是引用类型的初始值，不能赋值基本数据类型
           Integer integer = (Integer)null;
           System.out.println(integer);

           //自动拆箱报空指针
   //        int i = (Integer)null;
   //        System.out.println(i);

           //判断类型会返回false
           System.out.println(integer instanceof Integer);

           //静态方法没有报空指针    null-safe
           String s = String.valueOf(number);
           System.out.println(s);
           //toString()报空指针
   //        String s1 = number.toString();
   //        System.out.println(s1);

           //静态变量为null调用静态方法不会报空指针
       }
   }
   ```