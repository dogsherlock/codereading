##### javax.servlet.ServletRequestListener
```java
package javax.servlet;

import java.util.EventListener;

/**
 * ServletRequestListener用于监听ServletRequest对象的创建和销毁
 * 也就是监听请求从web应用进入和返回的事件
 * 当ServletRequest进入第一个servlet或者filter的时候是进入web应用的作用范围,
 * 从最后servlet或者调用链的第一个过滤器是退出web应用的作用范围
 *
 * 注册方式和HttpSessionAttributeListener类似
 */
public interface ServletRequestListener extends EventListener {

    /**
     * ServletRequest销毁时通知此监听器方法
     *
     * @param sre ServletRequestEvent类型src包含创建的ServletRequest和表示
     * 应用的ServletContext
     */
    default public void requestDestroyed(ServletRequestEvent sre) {}

    /**
     * ServletRequest创建时通知此监听器方法
     *
     * @param sre ServletRequestEvent类型src包含创建的ServletRequest和表示
     * 应用的ServletContext
     */
    default public void requestInitialized(ServletRequestEvent sre) {}
}
```