# 单例模式（Singleton Pattern）

描述：要求某个类只有唯一的实例，同时确保全局只有一个实例被创建。开发者使用该实例时，不需要手动创建，只需要调用类似`get_instance()`的方法获取该实例即可。

要求：
+ (1)单例的类只有一个实例
+ (2)单例的类公布获取实例的方法，并隐藏其他创建实例的方法（私有的构造函数）

例子：
+ (1)全局的id管理器
+ (2)数据库的连接

问题：
+ (1)`get_instance()`函数会出现被多个线程同时调用而多次创建的问题

## 实现

单例模式的实现主要分为**懒汉式**和**饿汉式**两种实现方法。

+ 懒汉式

懒汉式是指实例的创建在需要使用时。换句话说，只有`get_instance()`方法被调用，实例才会被创建。需要注意的是，由于可能多个线程同时访问`get_instance()`方法，需要加锁确保只有一个实例被创建。

```Java
public class Singleton{
  private static Singleton instance;
  private Singleton(){}

  public synchronized static Singleton getInstance(){
    if(instance == null){
      instance = new Singleton();
    }
    return instance;
  }
}
```

注意由于加锁操作的存在，懒汉式的实现存在一定的性能问题。不过只要`get_instance()`的方法调用频率不高问题不大。一种优化思路是(双重校验机制)：

```Java
public class Singleton{
  private static Singleton instance;
  private Singleton(){}

  public static Singleton getInstance(){
    if(instance == null){
      synchronized(Singleton.class){
        if(instance==null){
          instance = new Singleton();
        }
      }
    }
    return instance;
  }
}
```

+ 饿汉式

饿汉式是指，当程序初始化时就为单例类创建对象。这种方法的问题在于如果应用没有调用`get_instance()`的需求，程序依旧会创建一个实例占用存储空间，造成空间浪费。一种实现方法是：

```Java
public class Singleton{
  private static Singleton instance = new Singleton();
  private Singleton(){}

  public synchronized static Singleton getInstance(){
    return instance;
  }
}
```

这种方案基于`classloader`的双亲委派机制保证实例的唯一性。
