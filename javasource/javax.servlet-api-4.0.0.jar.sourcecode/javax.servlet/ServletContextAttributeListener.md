##### javax.servlet.ServletContextAttributeListener
```java
package javax.servlet;

import java.util.EventListener;

/**
 * 此接口用于ServletContext属性变化的事件通知
 * 注册方式和HttpSessionAttributeListener相同
 */
public interface ServletContextAttributeListener extends EventListener {

    /**
     * 当属性被加入ServletContext时，触发此方法
     *
     * @param event  ServletContextAttributeEvent 包含 加入属性的ServletContext对象,
     * 加入的属性name/value
     */
    default public void attributeAdded(ServletContextAttributeEvent event) {}

    /**
     * ServletContext移除属性时触发此方法
     * 
     * @param event  ServletContextAttributeEvent 包含 移除属性的ServletContext对象,
     * 移除的属性name/value
     */
    default public void attributeRemoved(ServletContextAttributeEvent event) {}

     /**
     * ServletContext替换属性时触发此方法
     * 
     * @param event  ServletContextAttributeEvent 包含 替换属性的ServletContext对象,
     * name/原始的value
     */
    default public void attributeReplaced(ServletContextAttributeEvent event) {}
}
```