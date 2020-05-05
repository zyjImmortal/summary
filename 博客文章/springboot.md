spring

spring MVC

spring boot

核心优势：

自动配置、到底是干什么的，解决了什么问题，比非自动配置有什么有有优势

IOC实现：容器、加入容器、注入

灵活性 ->多种多样的场景,



模式注解：

@Component 最多最基础的注解，如果一个类加上这个注解，被spring扫描到后，会加入到容器里

组件、类、bean，主要是围绕如何new进行

下面的这三个主要是表明类的不同作用

@Service

@Controller

@Repository



如果没有明确的目的就使用Component，



@Configuration



桥节点：



会根据变量名决定注入那个对象

Autowired注入的方式：

类型：

名字

