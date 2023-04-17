##### javax.servlet.ServletContainerInitializer
**ServletContainerInitializer**
是servlet3.0规范中引入的接口， 基于服务提供者接口（SPI）概念，
因此你需要在你的jar包目录下添加
META-INF/services/javax.servlet.ServletContainerInitializer
文件，内容就是ServletContainerInitializer实现类的全限定名.

例如在org.springframework:spring-web的META-INF/services中存在 
javax.servlet.ServletContainerInitializer文件，它的内容就是
springMVC提供的实现类org.springframework.web.SpringServletContainerInitializer.
我们在这个方法中通过编程的方式去注册Servlet Filter Listenner等组件，代替web.xml.

容器在启动应用的时候，会扫描当前应用每一个jar包里面
META-INF/services/javax.servlet.ServletContainerInitializer
指定的实现类，启动并运行这个实现类的方法

___

**tomcat启动流程**
1. 由tomcat在启动时根据SPI机制的ServiceLoader#load方法拿到所有
JavaEE接口（ServletContainerInitializer）注册的实现类。
2. Spring对该接口的实现类是SpringServletContainerInitializer，
其类上标注了@HandlesTypes({WebApplicationInitializer.class})。
3. tomcat从classpath下找到所有的WebApplicationInitializer实现类，将所有的实现类传入SpringServletContainerInitializer#onStartup方法的第一个参数，调用方法。
4. 回到SpringServletContainerInitializer#onStartup方法中的逻辑，
将所有的WebApplicationInitializer实现类的onStartup方法一一调用.
5. WebApplicationInitializer的实现类之一是AbstractDispatcherServletInitializer，
会创建spring容器、配置web.xml、注册过滤器.
当存在多个WebApplicationContext会设置其层级关系.

___

**@HandlesTypes注解原理**
* 含义
这也是Java EE规范中的注解，表示当前ServletContainerInitializer的实现类，
能处理的类型.

* 主要作用
Servlet容器（例如tomcat）启动时，会将SPI注册的Java EE接口ServletContainerInitializer
的所有的实现类(例如，SpringServletContainerInitializer)挨个回调其onStartup方法.
而onStartup方法是需要参数的，这时@HandlesTypes就派上用场了.onStartup方法所需要的参数
就通过@HandlesTypes注解传入.

* 实现原理
@HandlesTypes注解由Servlet容器提供支持（实现），参数中指定的所有实现类，利用字节码扫描框架
(例如ASM、BCEL)从classpath中扫描出来，放入集合，传给回调方法onStartup的第一个参数.

___

```java
package javax.servlet;

import java.util.Set;

/**
 * 此接口允许在web应用启动阶段，运行时库/第三方库通过编程的方式注册
 * servlet、filter、listener.
 * 
 * 此接口的实现可能会用javax.servlet.annotation.HandlesTypes注解，以便于
 * 在#onStartup方法中接受参数.
 * 
 * 如果此接口实现没有使用@HandlesTypes注解，或者匹配不到@HandlesTypes指定的应用类，
 * 容器必须传入null到#onStartup方法.
 *
 * 在扫描@HandlesTypes指定的所有实现类时可能会遇到类加载问题，如应用的jar文件缺失.
 * 此时需要忽略它们，同时提供一个可选配置来记录它们.
 *
 * 实现此接口的类要放置在jar包下META-INF/services/javax.servlet.ServletContainerInitializer
 * 目录中，然后使用运行时服务提供的查找机制或者容器指定的机制(反射、ASM、BCEL)，
 * ServletContaninerInitializer实现类必须符合双亲委派机制，并且查找后会被忽略顺序.
 */
public interface ServletContainerInitializer {

    /**
     * 由ServletContext表示的应用启动会通知ServletContainerInitializer实现类.
     *
     * 如果ServletContainerInitializer实现类时绑定在WEB-INF/lib第三依赖jar包中，
     * 则当捆绑的应用启动时实现类的onStartup方法只会调用一次.如果是在WEB-INF/lib
     * 目录之外，但仍然按照上述描述可以被发现，则实现类的onStartup方法会在应用
     * 每次启动的时候被调用.
     *
     * @param c 被@HandlesTypes注解的类以及其被继承实现的应用类集合.
     * 如果没有使用@HandlesTypes注解或者匹配不到，则为null.
     *
     * @param ctx web应用启动的ServletContext obj以及包含在c中的类.
     *
     * @throws ServletException 发生错误.
     */
    public void onStartup(Set<Class<?>> c, ServletContext ctx)
        throws ServletException; 
}
``` 