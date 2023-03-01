##### javax.servlet.http.HttpSessionBindingListener
```java
package javax.servlet.http;

import java.util.EventListener;
/**
 * 对于绑定在session的对象(此对象实现了HttpSessionBindListener接口)来说,
 * 当解绑或绑定的时候，这个对象会被通知
 * session绑定属性或者移除属性或者失效超时会触发,传递进来的事件实现了HttpSessionBindingEvent接口
 */

public interface HttpSessionBindingListener extends EventListener {

    /**
     * 当对象被绑定到session触发
     */ 
    default public void valueBound(HttpSessionBindingEvent event) {}

    /**
     * 当对象从session解除绑定时触发
     */
    default public void valueUnbound(HttpSessionBindingEvent event) {}
}
```