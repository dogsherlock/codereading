##### javax.servlet.ServletContextEvent
```java
package javax.servlet;

/** 
 * 一个web应用中servlet上下文变化的事件通知类
 */
public class ServletContextEvent extends java.util.EventObject { 

    private static final long serialVersionUID = -7501701636134222423L;

    /** 
     * 用给定的事件源ServletContext对象构造ServletContextEvent对象
     */
    public ServletContextEvent(ServletContext source) {
        super(source);
    }
    
    /**
     * 返回改变的ServletContext对象
     */
    public ServletContext getServletContext () { 
        return (ServletContext) super.getSource();
    }
}
```
