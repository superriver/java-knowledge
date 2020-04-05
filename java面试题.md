开源框架

一、IOC和DI是什么？
1、IOC（一种软件的设计思想）控制反转是把传统上由程序代码直接操控的对象的调用权交给容器，通过容器来实现对象组件的装配和管理。所谓的“控制反转”
就是对组件对象控制权的转移，从程序代码本身转移到了外部容器，由容器来创建对象并管理对象之间的依赖关系。
控制”就是指对 对象的创建、维护、销毁等生命周期的控制，这个过程一般是由我们的程序去主动控制的，如使用new关键字去创建一个对象（创建），在使用过程中保持引用（维护），在失去全部引用后由GC去回收对象（销毁）。
“反转”就是指对 对象的创建、维护、销毁等生命周期的控制由程序控制改为由IOC容器控制，需要某个对象时就直接通过名字去IOC容器中获取。


2、DI是IOC的一种重要实现，一个对象的创建往往会涉及到其他对象的创建，比如一个对象A的成员变量持有着另一个对象B的引用，这就是依赖
A依赖于B。IOC机制既然负责了对象的创建，那么这个依赖关系也就必须由IOC容器负责起来。负责方式就是DI——依赖注入，通过将依赖关系写入配置文件，
然后在创建有依赖关系的对象时，由IOC容器注入依赖的对象，如在创建A时，检查到有依赖关系，IOC容器就把A依赖的对象B创建后注入到A中（组装，通过反射机制实现），然后把A返回给对象请求者，完成对象。

3、IOC的意义何在？
IOC并没有实现更多的功能，但它的存在使我们不需要很多代码、不需要考虑对象间复杂的耦合关系就能从IOC容器中获取合适的对象，而且提供了对 对象的可靠的管理，极大地降低了开发的复杂性。

二、Spring IOC的理解，其初始化过程？
IOC容器的初始化包括BeanDefinition的Resource定位、载入和注册这三个基本的过程。

三、BeanFactory和FactoryBean的区别
  1、 BeanFactory定义了IOC容器的最基本形式，并提供了IOC容器应遵守的最基本的接口，也就是Spring IOC所遵守的最底层和最基本的编程规范。
  它的职责包括：实例化、定位、配置应用程序中的对象及建立这些对象间的依赖。
  在Spring代码中，BeanFactory只是接口，并不是IOC容器的具体实现，
	但是Spring容器给出了很多种实现，如DefaultListableBeanFactory、XmlBeanFactory、ApplicationContext等，都是附加了某种功能的实现。
	
  2、一般情况下，Spring通过反射机制利用<bean>的class属性指定实现类实例化Bean,在某些情况下，实例化Bean过程比较复杂，
如果按照传统的方式，则需要在<bean>中提供大量的配置信息，配置方式的灵活性是受限的，这时采用编码的方式可能会得到一个简单的方案。
Spring为此提供了一个org.springframework.bean.factory.FactoryBean的工厂类接口，用户可以通过实现该接口定制实例化Bean的逻辑。
FactoryBean接口对于Spring框架来说占用重要的地位，Spring自身就提供了70多个FactoryBean的实现。
它们隐藏了实例化一些复杂Bean的细节，给上层应用带来了便利。从Spring3.0开始，FactoryBean开始支持泛型，即接口声明改为FactoryBean<T>的形式。
	FactoryBean开始支持泛型，即接口声明改为FactoryBeanactoryBean是工厂类接口，当你只是想简单的去构造Bean，不希望实现原有大量的方法。它是一个Bean，不过这个Bean能够做为工厂去创建Bean，同时还能修饰对象的生成。
FactoryBean比BeanFactory在生产Bean的时候灵活，还能修饰对象，带有工厂模式和装饰模式的意思在里面，不过它的存在还是以Bean的形式存在。

四、ApplicationContext生命周期
Bean被载入到容器中时，他的生命周期就开始
Bean工厂在一个Bean可以使用前完成很多工作：
1、容器寻找Bean的定义信息实例化
2、使用依赖注入，Spring按Bean定义信息配置Bean的所有属性
3、若Bean实现了BeanNameAware接口，工厂调用Bean的setBeanName()方法BeanID.
4、若Bean实现了BeanFactoryAware接口，工厂调用setBeanFactory()方法传入工厂自身。
5、若BeanPostProcessor和Bean关联，则它们的postProcessBeforeInitialization()方法调用。
6、若bean指定了ini-method方法、，它将被调用。
7、最后，若有BeanPostProcessor和bean关联，则它们的postProcessAfterInitialization()方法被调用。

五、Spring Bean的生命周期

1、instantiate bean 对象实例化
2、populate properties 封装属性
3、如果Bean实现 BeanNameAware执行setBeanName
4、如果Bean实现BeanFactoryAware或者ApplicationContextAware设置工厂setBeanFactory或者上下文对象setApplicationContext
5、如果存在类实现BeanPostProcessor（后处理Bean），执行postProcessBeforeInitialization
6、如果Bean实现InitializingBean执行AfterPropertiesSet
7、调用<bean init-method="init">指定初始化方法init，如果存在类实现BeanPostProcessor(处理Bean),执行postProcessAfterInitialization
8、执行业务处理
9、如果Bean实现DisposableBean，执行destory
10、调用<bean destroy-method="customerDestroy"> 指定销毁方法customerDestroy

六、Spring Aop的实现原理

七、Spring如何解决循环依赖？
Spring实例化bean是通过ApplicationContext.getBean()方法来进行的。如果要获取的对象依赖了另一个对象，那么其首先会创建当前对象，
然后通过递归的调用ApplicationContext.getBean()方法来获取所依赖的对象，最后将获取到的对象注入到当前对象中。
这里我们以上面的首先初始化A对象实例为例进行讲解。首先Spring尝试通过ApplicationContext.getBean()方法获取A对象的实例，
由于Spring容器中还没有A对象实例，因而其会创建一个A对象，然后发现其依赖了B对象，因而会尝试递归的通过ApplicationContext.getBean()方法获取B对象的实例，
但是Spring容器中此时也没有B对象的实例，因而其还是会先创建一个B对象的实例。读者需要注意这个时间点，此时A对象和B对象都已经创建了，
并且保存在Spring容器中了，只不过A对象的属性b和B对象的属性a都还没有设置进去。在前面Spring创建B对象之后，Spring发现B对象依赖了属性A，
因而此时还是会尝试递归的调用ApplicationContext.getBean()方法获取A对象的实例，因为Spring中已经有一个A对象的实例，
虽然只是半成品（其属性b还未初始化），但其也还是目标bean，因而会将该A对象的实例返回。此时，B对象的属性a就设置进去了，
然后还是ApplicationContext.getBean()方法递归的返回，也就是将B对象的实例返回，此时就会将该实例设置到A对象的属性b中。这个时候，
注意A对象的属性b和B对象的属性a都已经设置了目标对象的实例了。读者朋友可能会比较疑惑的是，前面在为对象B设置属性a的时候，
这个A类型属性还是个半成品。但是需要注意的是，这个A是一个引用，其本质上还是最开始就实例化的A对象。而在上面这个递归过程的最后，
Spring将获取到的B对象实例设置到了A对象的属性b中了，这里的A对象其实和前面设置到实例B中的半成品A对象是同一个对象，其引用地址是同一个，
这里为A对象的b属性设置了值，其实也就是为那个半成品的a属性设置了值。
https://my.oschina.net/zhangxufeng/blog/3096394
https://zhuanlan.zhihu.com/p/84267654
https://www.imooc.com/article/34150

八、spring如何保证Controller并发的安全？
1.不要在controller中定义成员变量
2、万一必须要定义一个非静态成员变量的时候，则通过注解@Scope("prototype"),将其设置为多列模式。
3、在Controller中使用ThreadLocal变量

spring bean作用域有以下5个：

singleton:单例模式，当spring创建applicationContext容器的时候，spring会欲初始化所有的该作用域实例，加上lazy-init就可以避免预处理；

prototype：原型模式，每次通过getBean获取该bean就会新产生一个实例，创建后spring将不再对其管理；

（下面是在web项目下才用到的）

request：搞web的大家都应该明白request的域了吧，就是每次请求都新产生一个实例，和prototype不同就是创建后，接下来的管理，spring依然在监听；

session:每次会话，同上；

global session:全局的web域，类似于servlet中的application。


九、Spring如何管理事务的，事务管理机制？

十、Spring的不同事务传播行为有哪些？干什么用的？


并发编程
一、跟Synchronized相比，可重入锁ReentrantLock其实现原理有什么不同？

二、ReentrantLock是如何实现可重入性的？
ReentranLock通过对state属性进行加减操作实现可重入的功能，对获取不到锁的线程进行了自旋操作
https://www.jianshu.com/p/797c7f2b4d82

三、volatile与Synchronized异同


1)volatile比synchronized更轻量级。

2)volatile没有synchronized使用的广泛。

3)volatile不需要加锁，比synchronized更轻量级，不会阻塞线程。

4)从内存可见性角度看，volatile读相当于加锁，volatile写相当于解锁。

5)synchronized既能保证可见性，又能保证原子性，而volatile只能保证可见性，无法保证原子性。

6)volatile本身不保证获取和设置操作的原子性，仅仅保持修改的可见性。但是java的内存模型保证声明为volatile的long和double变量的get和set操作是原子的。

synchronize实现的锁本质上是一种阻塞锁，也就是说多个线程要排队访问同一个共享对象。
而volatile是Java虚拟机提供的一种轻量级同步机制，他是基于内存屏障实现的。说到底，他并不是锁，所以他不会有synchronized带来的阻塞和性能损耗的问题。

四、Java中的线程池是如何实现的？
1、创建线程池的目的：
资源是有限，不可能无限制的创建线程和销毁线程，这样会导致大量的资源销毁和性能降低。而且这些线程缺乏统一的管理，也缺乏定期执行，定时执行，线程中断的功能。
2、线程池的好处
1)重用已经存在的线程，减少了线程的创建和销毁的开销
2)可有效控制最大并发的线程数，提高了系统资源的使用率避免很多竞争，避免了OOM啊死锁啊等。
3)可以提供定时和定期的执行方式，单线程，并发数量的控制等功能!

3、构造函数

如果把线程池比作一个公司。公司会有正式员工处理正常业务，如果工作量大的话，会雇佣外包人员来工作。

闲时就可以释放外包人员以减少公司管理开销。一个公司因为成本关系，雇佣的人员始终是有最大数。

如果这时候还有任务处理不过来，就走需求池排任务。

acc : 获取调用上下文
corePoolSize: 核心线程数量，可以类比正式员工数量，常驻线程数量。
线程池中的核心线程数，当提交一个任务时，线程池创建一个新线程执行任务，直到当前线程数等于corePoolSize, 即使有其他空闲线程能够执行新来的任务, 也会继续创建线程；如果当前线程数为corePoolSize，继续提交的任务被保存到阻塞队列中，等待被执行；如果执行了线程池的prestartAllCoreThreads()方法，线程池会提前创建并启动所有核心线程。

maximumPoolSize: 最大的线程数量，公司最多雇佣员工数量。常驻+临时线程数量。

workQueue：多余任务等待队列，再多的人都处理不过来了，需要等着，在这个地方等。
线程池中允许的最大线程数。如果当前阻塞队列满了，且继续提交任务，则创建新的线程执行任务，前提是当前线程数小于maximumPoolSize；当阻塞队列是无界队列, 则maximumPoolSize则不起作用, 因为无法提交至核心线程池的线程会一直持续地放入workQueue.

keepAliveTime：非核心线程空闲时间，就是外包人员等了多久，如果还没有活干，解雇了。
线程空闲时的存活时间，即当线程没有任务执行时，该线程继续存活的时间；默认情况下，该参数只在线程数大于corePoolSize时才有用, 超过这个时间的空闲线程将被终止；

threadFactory: 创建线程的工厂，在这个地方可以统一处理创建的线程的属性。每个公司对员工的要求不一样，恩，在这里设置员工的属性。
创建线程的工厂，通过自定义的线程工厂可以给每个新建的线程设置一个具有识别度的线程名。默认为DefaultThreadFactory

handler：线程池拒绝策略，什么意思呢?就是当任务实在是太多，人也不够，需求池也排满了，还有任务咋办?默认是不处理，抛出异常告诉任务提交者，我这忙不过来了。
线程池的饱和策略，当阻塞队列满了，且没有空闲的工作线程，如果继续提交任务，必须采取一种策略处理该任务，线程池提供了4种策略：
AbortPolicy：直接抛出异常，默认策略；
CallerRunsPolicy：用调用者所在的线程来执行任务；
DiscardOldestPolicy：丢弃阻塞队列中靠最前的任务，并执行当前任务；
DiscardPolicy：直接丢弃任务；
当然也可以根据应用场景实现RejectedExecutionHandler接口，自定义饱和策略，如记录日志或持久化存储不能处理的任务。


4、工作原理
当一个任务提交至线程池之后，
1) 线程池首先判断核心线程池里的线程是否已经满了。如果不是，则创建一个新的工作现场来执行任务，否则进入2
2) 判断工作队列是否已经满了，倘若还没有满，将线程放入工作队列。否则进入3.
3) 判断线程池里的线程是否都在执行任务。如果不是，则创建一个新的工作线程来执行。如果线程池满了，则交给饱和策略来处理任务。

https://www.jianshu.com/p/9b7dfb407f72

https://www.cnblogs.com/jyroy/p/11365935.html

数据库：

一、事务的四大特性
1、原子性
2、一致性
3、隔离性
4、持续性

二、MySQL的四种隔离级别
1、读未提交  :引发脏读数据
2、读提交 : 能解决脏读的问题，但是却会引发一个事务范围内两个相同的查询却返回了不同数据，这就算不可重复读。

3、重复读：就是在开始读取数据时，不可修改操作。但会引发幻读问题。
4、序列化：Serializable 是最高的事务隔离级别，在该级别下，事务串行化顺序执行，可以避免脏读、不可重复读与幻读。但是这种事务隔离级别效率低下，比较耗数据库性能，一般不使用。
值得一提的是：大多数数据库默认的事务隔离级别是Read committed，比如Sql Server , Oracle。Mysql的默认隔离级别是Repeatable read。

https://blog.csdn.net/riemann_/article/details/89901626

三、MyISAM和 INNODB的区别是什么？
https://blog.csdn.net/itcats_cn/article/details/82154600?depth_1-utm_source=distribute.pc_relevant.none-task&utm_source=distribute.pc_relevant.none-task
MyISAM

