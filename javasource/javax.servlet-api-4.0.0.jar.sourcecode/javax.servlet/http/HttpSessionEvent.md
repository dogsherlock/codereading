##### javax.servlet.http.HttpSessionEvent
```java
package javax.servlet.http;

/**
 * 此类代表在一个web应用中session变化的事件
 */
public class HttpSessionEvent extends java.util.EventObject {

    private static final long serialVersionUID = -7622791603672342895L;

    /**
     * 用给定的事件源source构造一个session事件
     */
    public HttpSessionEvent(HttpSession source) {
        super(source);
    }

    /**
     * 返回变化的session
     */
    public HttpSession getSession () { 
        return (HttpSession) super.getSource();
    }
}
```