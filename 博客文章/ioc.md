# 先从一段脏乱差的代码开始

作为一个LOL资深黄铜，本着只会放R技能的原则，就以LOL来举例，如果玷污了各位心目中的英雄，那就对不起了啊。比如说我现在想要程序能够根据输入的英雄名字自动调用英雄的R技能该怎么 做呢.

终于，在苦思冥想一个礼拜后，写下了如下代码，随之疯狂起舞：

先写几个英雄类，每个类都有四个方法，也就是英雄的四个技能

```java
public class Camille {
    private String skillName = "Camille R";

    public Camille() {
        System.out.println("Hello,Camille");
    }
  
  public void q(){
        System.out.println("Camille Q");
    }

    public void w(){
        System.out.println("Camille W");
    }

    public void e(){
        System.out.println("Camille E");
    }

    public void r(){
        System.out.println(this.skillName);
    }
}
```

```java
public class Diana {
    private String skillName = "Diana R";
    public Diana() {
        System.out.println("Hello,Diana");
    }

    public void q(){
        System.out.println("Diana Q");
    }

    public void w(){
        System.out.println("Diana W");
    }

    public void e(){
        System.out.println("Diana E");
    }

    public void r(){
        System.out.println(this.skillName);
    }
}
```

```java
public class Irelia {

    public Irelia() {
        System.out.println("Hello, Irelia");
    }
    public void q(){
        System.out.println("Irelia Q");
    }

    public void w(){
        System.out.println("Irelia W");
    }

    public void e(){
        System.out.println("Irelia E");
    }

    public void r(){
        System.out.println("Irelia R");
    }
}
```

然后就根据英雄的名字，选择实例对象，然后调用R节能，不对，R方法。

```java
public class Test {
    public static void main(String[] args) throws Exception{
        String name = "Irelia";

        switch (name){
            case "Diana":
                Diana diana = new Diana();
                diana.r();
                break;
            case "Irelia":
                Irelia irelia = new Irelia();
                irelia.r();
                break;
            case "Camille":
                Camille camille = new Camille();
                camille.r();
                break;
            default:
                throw new Exception("查无此人");
        }
    }
}
```

欣欣然，终于写出来了。

但是想来想去总觉得有点问题，为什么要有那么多的new，为什么要有那么多的r方法调用，都叫r，就不能只调一次么,那有什么办法能够将相同方法的调用统一处理呢，那就一定是接口了。

# 接口：统一方法的调用

既然都是英雄，都有那四个名字一样的技能，那岂不是可以用接口定义一下行为，然后具体每个对象都实现这个接口和实现接口定义的方法，那我调用的时候只写一个调用不就行了，下面试着写了下：

先定义一个叫Skill的Interface

```java
public interface Skill  {
    void q();
    void w();
    void e();
    void r();
}
```

上面的类都实现一下这个接口，然后主要来看一下具体使用的时候，是如何做的。

```java
public class Test {
    public static void main(String[] args) throws Exception{
        String name = "Irelia";
        Skill hero;
        switch (name){
            case "Diana":
                hero= new Diana();
                break;
            case "Irelia":
                hero = new Irelia();
                break;
            case "Camille":
                hero = new Camille();
                break;
            default:
                throw new Exception("查无此人");
        }
        hero.r();
    }
}
```

可以看到上面我们通过接口统一了方法的调用，只需要在不同的条件下new出不同的对象即可，最后在调用r方法。

但是看起来还是有点问题，interface虽然统一了方法的调用，但是不能统一对象的实例化。如果在其他地方也需要根据传入的英雄名去实例化一个英雄对象的话，那就仍然需要写这一大坨代码，如果每新增一个英雄都在改一下，那这样就太麻烦了。

那如果能有一个地方能够统一的负责对象的创建，我们只需要传入要实例化英雄的名字，就返回这个英雄的对象，那就方便了，英雄发生变化到的时候，只需在这一个地方修改就好了。

好像貌似写代码，就是在做两件事，对象实例化，和方法调用，只不过区别就在于怎么样能够更好的更方便更稳定的进行对象实例化和方法调用。

# 工厂模式

在设计模式中，一般分为三大类创建型、结构型和行为型，工厂模式就是创建型当中的一种。工厂顾名思义，就是说根据要求生成出合格到的产品，那对于软件编程来说，就是通过抽象工厂的形式来创建出符合要求的对象。

工厂模式一般有三种实现方式：简单工厂、工厂方法、抽象工厂，这里我们用简单工厂的形式来解决上文提到的统一对象实例化的问题。

将上文的switch case那段代码独立到一个类中，这个类只负责对象的创建，如下示例：

```java
public class HeroFactory {
    public static Skill getHero(String heraName) throws Exception{
        Skill hero;
        switch (heraName){
            case "Diana":
                hero= new Diana();
                break;
            case "Irelia":
                hero = new Irelia();
                break;
            case "Camille":
                hero = new Camille();
                break;
            default:
                throw new Exception("查无此人");
        }

        return hero;
    }
    }
```

只需传入英雄的名字，`getHero`方法就会返回特定的对象，在任何想要获取对象的地方，只需要调用这个方法即可，而且即使英雄发生变化，也不会影响调用方，这样就做到了对象的统一实例化。相当于把代码变化的部分或者说是不稳定的地方独立出来，让使用的地方都处于稳定状态。

那这样就完美了么，显然不是，如果我们需要频繁的新增英雄或者英雄的名字发生了变化，那这里的代码就要频繁的修改，而且英雄种类过多的话，swi语句会相当的庞大，有没有一种方式不用switch语句，就能根据我传入的英雄的名字创建出对象呢？

# 反射

在一般操作数据的时候，是依赖于数据类型的，而且这都是已知的，比如根据类型new一个对象，根据类型定义变量，类型可能是基本类型、接口或者数组等，编译器也是根据类型进行代码的检查的。

而反射是不一样的，它是在运行时而非编译时，动态获取类型的信息，包括接口信息、成员信息、构造方法信息等，可以根据动态获取到的信息来创建对象或者访问修改成员、调用方法等。

上文我们提出问题，能够根据传入的类名，来创建对象，从而优化掉那段烦人的switch语句，这里我们给出解决方案，通过反射来创建对象，不过这不是传入一个简单的类名，而是要创建对象的全限定类名。

```java
String heraName = "com.zyj.missyou.factory.hero.Irelia";
public static Skill getHeroByReflect(String heraName) throws Exception{;
    Class<?> tClass = Class.forName(heraName);
    Object obj = tClass.newInstance();
    return (Skill) obj;
}
```

先来解释一下其中的一些知识点，每个已加载的类在内存中都会存有一份类信息，类信息对应的类就是java.lang.Class，它是一个泛型类，这个Class类提供了一个静态方法forName，能够根据传入的全限定类名获取一个类的类对象。通过类对象的newInstance方法，创建这个类的实例对象。

这样我们通过反射的方式就处理掉了那段switch代码，这样代码就看起来整洁多了，而且最重要的是，这段代码变得稳定了，他不会在因为英雄发生变化而不停的来这里修改代码了。

# 总结

反过来看，其实我们不断的优化代码的过程，就是围绕着一个代码设计原则来做，开闭原则，对修改封闭，对扩展开开放，我们在设计类、模块，系统架构都需要用得到这个原则。