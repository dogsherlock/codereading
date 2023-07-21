##### javax.servlet.SingleThreadModel
```java
package javax.servlet;

/**
 * 确保servlet同一时间只处理一个请求，此接口没有方法.
 *
 * 如果servlet实现了此接口，你必须确保没有两个线程并发执行servlet的service方法.
 * servlet容器可以确保通过同步访问一个servlet实例，或者维护servlet实例池，
 * 将新的请求分发给空闲的servlet.
 *
 * 注意此接口不能解决所有的线程安全问题，如，即使使用了实现此接口的
 * servlet，还是可以在同一时间使用多个线程处理多个请求，访问session的属性以及
 * 静态变量.推荐开发者使用其它的方法来解决此问题，而不是实现此接口.
 * 如防止使用实例变量，或者访问临界资源的时候同步代码块.
 *
 * @deprecated	截止到Java Servlet API 2.4, 无替换的接口.
 */
@Deprecated
public interface SingleThreadModel {
}
```