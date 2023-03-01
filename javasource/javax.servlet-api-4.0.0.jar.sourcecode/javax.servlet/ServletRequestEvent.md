##### javax.servlet.ServletRequestEvent
```java
package javax.servlet;

/** 
 * ServletRequestEvent表示一个ServletRequest对象生命周期的事件，事件源为web应用的
 * ServletContext
 */
public class ServletRequestEvent extends java.util.EventObject { 

    private static final long serialVersionUID = -7467864054698729101L;

    private final transient ServletRequest request;

    /** 
      * 用给定的ServletContext对象和ServletRequest对象构造一个ServletRequestEvent事件对象
      */
    public ServletRequestEvent(ServletContext sc, ServletRequest request) {
        super(sc);
        this.request = request;
    }

    /**
      * 返回正在变化的ServletRequest
      */
    public ServletRequest getServletRequest () { 
        return this.request;
    }

    /**
      * 返回此web应用的ServletContext
      */
    public ServletContext getServletContext () { 
        return (ServletContext) super.getSource();
    }
}
```