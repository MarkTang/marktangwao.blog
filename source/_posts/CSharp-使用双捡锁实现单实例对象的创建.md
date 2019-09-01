---
title: 'C#使用双捡锁实现单实例对象的创建'
date: 2019-08-11 14:25:59
category: [Backend]
tags: [.NET Standard, C#]
description: 
---



双捡锁(Double Check Locking)是一个非常著名的技术，开发人员用它将单实例(Singleton)对象的构造推迟到应用程序首次请求该对象时进行。有时也成为**延迟初始化(Lazy Initialization)**。如果应用程序永远不请求对象，对象也就永远不会构造，从而节省了时间和内存。单当多个线程同事请求单实例对象时就可能出现问题。这个时候必须使用一些线程同步机制确保单实例对象只被构造一次。



##### 1、使用static修饰符(没有使用双捡锁技术)

~~~c
public sealed class SingletonSimple
{
    private static SingletonSimple _instance = new SingletonSimple();

    // Private constructor prevents any code outside this class from creating an instance
    private SingletonSimple()
    {
        // Code to initialize the one Singleton object goes here...
    }

    // Public, static method that returns the Singleton object (creating it if necessary)
    public static SingletonSimple GetSingleton()
    {
        return _instance;
    }
}
~~~

代码首次访问类的成员时，CLR会自动调用类型的类构造器，所以首次有一个线程查询**SingletonSimple**的**GetSingleton**方法时，CLR就会自动调用类构造器，从而创建一个对象实例。

此外，CLR保证了对类构造器的调用时线程安全的。因为CLR希望确保在每个AppDomain中，一个类型构造器只执行一次。为了保证这一点，在调用类型构造器时，调用线程要获取一个**互斥线程同步锁**。这样一来，如果多个线程试图同时调用某个类型的类型构造器，只有一个线程才可以获得锁，其他线程会被阻塞(blocked)。第一个线程会执行类型构造器中的代码。当第一个线程离开构造器后，正在等待的线程将被唤醒，然后发现构造器的代码已被执行过。因此，这些线程不会再次执行代码，将直接从构造器方法返回。

除此之外，如果再次调用这样的一个方法，CLR知道类型构造器已被执行过，从而确保类型构造器不被再次调用。

但这种创建单实例方式的缺点在于，首次访问类的任何成员都会调用类型构造器。所以，如果**SingletonSimple**类型定义了其他静态成员，就会在访问其他任何静态成员时创建**SingletonSimple**对象。

解决这种方式的缺点，请继续往下看，第二，第三种创建单实例的方法；



##### 2、使用Monitor

~~~c
public sealed class SingletonMonitor
{
    // _lock is required for thread safety and having this object assumes that creating
    // the singleton object is more expensive than creating a System.Object object and that
    // creating the singleton object may not be necessary at all. Otherwise, it is more
    // efficient and easier to just create the singleton object in a class constructor
    private static readonly object _lock = new object();

    // This field will refer to the one Singleton object
    private static SingletonMonitor _instance = null;

    // Private constructor prevents any code outside this class from creating an instance
    private SingletonMonitor()
    {
        // Code to initialize the one Singleton object goes here...
    }

    // Public, static method that returns the Singleton object (creating it if necessary)
    public static SingletonMonitor GetSingleton()
    {
        // If the Singleton was already created, just return it (this is fast)
        if (_instance != null)
        {
            return _instance;
        }

        // Not created, let 1 thread create it
        Monitor.Enter(_lock);
        if (_instance == null)
        {
            // Still not created, create it
            SingletonMonitor singleton = new SingletonMonitor();

            // Save the reference in _instance (see discussion for details)
            Volatile.Write(ref _instance, singleton);
        }
        Monitor.Exit(_lock);

        // Return a reference to the one Singleton object
        return _instance;
    }

}
~~~

双捡锁技术背后的思路在于，对**GetSingleton**方法的一个调用可以快速地检查**_instance**字段，判断对象是否创建。如果是，方法就返回对它的引用。这里的妙处在于，如果对象已经构造好，就不需要线程同步，应用程序会运行得非常快。另一方面，如果调用**GetSingleton**方法的第一个线程发现对象还没有创建，就会获取一个线程同步锁来确保只有一个线程构造单实例对象。这意味着只有线程第一次查询单实例对象时，才会出现性能上的损失。

**GetSingleton**内部有一个**Volatile.Write**调用。下面让我来解释一下它解决的是什么问题。

假定第二个**if**语句中包含的是下面这行代码：

~~~c
_instance = new SingletonMonitor();
~~~

你的想法是让编译器生成代码为一个**SingletonMonitor**分配内存，调用构造器来初始化字段，再将引用赋给**_instance**字段。使一个值对其他线程可见称为**发布(publishing)**。但那只是你一厢情愿的想法，编译器可能这样做：为**SingletonMonitor**分配内存，将引用发布到(赋给)**_instance**，再调用构造器。从单线程的角度出发，像这样改变顺序是无关紧要的。但在将应用发布给**_instance**之后，并在调用构造器之前，如果另一个线程调用了**GetSingleton**方法，但对象的内部构造器还没有结束执行呢！这是一个很难追踪的bug，尤其是它完全由于计时而造成的。

对**Volatile.Write**的调用修正了这个问题。它保证**singleton**中的引用只有在构造器结束执行之后，才发布到**_instance**中。

解决这个问题的另一个办法是使用C#的**volatile**关键字来标记**_instance**字段。这使向**_instance**的写入变得具有“易变性”。同样，构造器必须在写入发生前结束运行。但遗憾的是，这同时会使所有读取操作具有“易变性”，这是完全没必要的。因此，使用**volatile**关键字，会使性能无所谓地受到损害。



##### 3、使用Interlocked

~~~c
public sealed class SingletonInterlocked
{
    private static SingletonInterlocked _instance = null;

    // Private constructor prevents any code outside this class from creating an instance
    private SingletonInterlocked()
    {
        // Code to initialize the one Singleton object goes here...
    }

    // Public, static method that returns the Singleton object (creating it if necessary)
    public static SingletonInterlocked GetSingleton()
    {
        if (_instance != null)
        {
            return _instance;
        }

        // Create a new Singleton and root it if another thread didn't do it first
        SingletonInterlocked singleton = new SingletonInterlocked();

        Interlocked.CompareExchange(ref _instance, singleton, null);

        // If this thread lost, then the second Singleton object gets GC'd
        // Return reference to the single object
        return _instance; 
    }
}
~~~

如果多个线程同时调用**GetSingleton**，这中方式可能创建两个或是更多**SingletonInterlocked**对象。然而对**Interlocked.CompareExchange**的调用确保只有一个引用才会发布到**_instance**字段中。没有通过这个字段固定下来的任何对象会在以后被垃圾回收。由于大多数应用程序都很少发生多个线程同时调用**GetSingleton**的情况，所以不太可能同时创建多个**SingletonInterlocked**对象.

虽然可能创建多个**SingletonInterlocked**对象，但上述代码有多方面的优势。首先，它的速度非常快。其次，它永不阻塞线程。

相反，如果一个线程池线程在一个**Monitor**或者其他任何内核模式的线程同步构造上阻塞，线程池就会创建另一个线程来保持**CPU**的“饱和”。因此，会分配并初始化更多的内存，而且所有DLL都会收到一个线程连接通知。使用**CompareExchange**则永远不会发生这种情况。当然，只有在构造器没有副作用的时候才能使用这中方式。

















