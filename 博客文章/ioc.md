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

但是想来想去总觉得有点问题，为什么要有那么多的new，为什么要有那么多的r方法调用，都叫r，就不能只调一次么。

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

可以看到上面我们通过接口统一了方法的调用，是这个调用类的代码行数减少挺多，但是这里还有一个问题，对象的实例化能够怎么处理呢，interface虽然统一了方法的调用，但是不能统一对象的实例化。

好像貌似写代码，就是在做两件事，对象实例化，和方法调用，只不过区别就在于怎么样能够更好的更方便更稳定的进行对象实例化和方法调用。

