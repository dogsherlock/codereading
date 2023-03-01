##### javax.servlet.ServletRequestAttributeListener
```java
package javax.servlet;

import java.util.EventListener;

/**
 * 此接口用于接收ServletRequest对象属性变化的事件通知
 *
 * 注册方式和HttpSessionAttributeListener类似
 */
public interface ServletRequestAttributeListener extends EventListener {

    /**
     * 监听属性新增
     */
    default public void attributeAdded(ServletRequestAttributeEvent srae) {}

    /**
     * 监听属性删除
     */
    default public void attributeRemoved(ServletRequestAttributeEvent srae) {}

    /**
     * 监听属性替换
     */
    default public void attributeReplaced(ServletRequestAttributeEvent srae) {}
}
```