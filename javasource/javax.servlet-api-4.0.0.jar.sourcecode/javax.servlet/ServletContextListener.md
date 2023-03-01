##### javax.servlet.ServletContextListener
```java
package javax.servlet;

import java.util.EventListener;

/** 
 * ServletContextListener接口用于监听ServletContext对象的生命周期变化事件
 * 当Servlet容器启动或终止Web应用时,会触发ServletContextEvent事件.该事件由
 * ServletContextListener来处理
 * 
 * 注册方式和HttpSessionAttributeListener类似
 */
public interface ServletContextListener extends EventListener {

    /**
     * 当Servlet容器启动Web应用时调用该方法 在调用完该方法之后，容器再对
     * Filter初始化，并且对那些在Web应用启动时就需要被初始化的Servlet进行
     * 初始化
     */
    default public void contextInitialized(ServletContextEvent sce) {}

    /**
     * 当Servlet容器终止Web应用时调用该方法,在调用该方法之前，容器会先
     * 销毁所有Servlet和Filter过滤器
     */
    default public void contextDestroyed(ServletContextEvent sce) {}
}
```