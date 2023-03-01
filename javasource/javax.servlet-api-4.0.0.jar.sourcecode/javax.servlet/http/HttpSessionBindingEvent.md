##### javax.servlet.http.HttpSessionBindingEvent
```java
package javax.servlet.http;

/**
 * 这个事件要么会发送到从session绑定或解绑的对象,此对象需实现HttpSessionBindingListener接口
 * 如果session属性发生了变化(HttpSession.setAttribute、HttpSession.removeAttribute),
 * 则也会发给实现了HttpSessionAttributeListener的对象
 */
public class HttpSessionBindingEvent extends HttpSessionEvent {

    private static final long serialVersionUID = 7308000419984825907L;

    // 被绑定或者解绑的对象name 
    private String name;
    
    // 被绑定的对象
    private Object value;
    
    /**
     * 构造event obj
     * 
     * @param session 解绑或绑定对象的session
     * @param name 解绑或绑定对象的name
     */
    public HttpSessionBindingEvent(HttpSession session, String name) {
        super(session);
        this.name = name;
    }
    
    /**
     * 构造event obj
     */
    public HttpSessionBindingEvent(HttpSession session, String name, Object value) {
        super(session);
        this.name = name;
        this.value = value;
    }
    
    /**
     * 返回改变的session
     */
    @Override
    public HttpSession getSession () { 
        return super.getSession();
    }
 
    /**
     * 返回name
     */
    public String getName() {
        return name;
    }
    
    /**
     * 被移除、添加、替换的对象
     */
    public Object getValue() {
        return this.value;   
    }
}
```