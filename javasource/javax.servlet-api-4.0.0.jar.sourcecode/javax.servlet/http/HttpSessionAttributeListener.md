##### javax.servlet.http.HttpSessionAttributeListener
```java
package javax.servlet.http;

import java.util.EventListener;

/**
 * HttpSession属性变化后，接受事件通知
 * 为了接收事件通知，实现类需要做到如下任意一点:
 * 1. 在web.xml中配置,并实现HttpSessionAttributeListener接口
 * <listener><listener-class>全限定名<listener-class><listener>
 * 2. 使用javax.servlet.annotation.WebListener注解
 * 3. 通过javax.servlet.ServletContext的addListener方法加入
 */
public interface HttpSessionAttributeListener extends EventListener {

    /**
     * 用于session加入属性时接受通知
     *
     * @param event HttpSessionBindingEvent 包含当前的session和加入的name/value 
     */
    default public void attributeAdded(HttpSessionBindingEvent event) {}

    /**
     * 用于session移除属性时接受通知
     *
     * @param event HttpSessionBindingEvent 包含当前的session和移除的name/value 
     */
    default public void attributeRemoved(HttpSessionBindingEvent event) {}

    /**
     * 绑定在session的属性被替换时接受通知
     *
     * @param event  HttpSessionBindingEvent 包含当前的session和name/原始被替换的value
     */
    default public void attributeReplaced(HttpSessionBindingEvent event) {}
}
```