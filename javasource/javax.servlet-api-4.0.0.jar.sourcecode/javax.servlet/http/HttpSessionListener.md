##### javax.servlet.http.HttpSessionListener
```java
package javax.servlet.http;

import java.util.EventListener;

/** 
 * HttpSessionListener用于接受HttpSession生命周期变化的事件
 * 即可以监听当前应用中session的创建和销毁情况
 * 注册方式和HttpSessionAttributeListener相同
 */
public interface HttpSessionListener extends EventListener {
    
    /** 
     * 监听session创建的事件
     */
    default public void sessionCreated(HttpSessionEvent se) {}
    
    /** 
     * 监听session销毁的事件
     */
    default public void sessionDestroyed(HttpSessionEvent se) {}
}
```