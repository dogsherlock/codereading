##### javax.servlet.http.HttpSession
**session进行身份验证的原理**
> 当客户端第一次访问服务器的时候，此时客户端的请求中不携带任何标识给服务器，
> 所以此时服务器无法找到与之对应的 session，所以会新建session对象，当服务器
> 进行响应的时候，服务器会将session标识放到响应头的Set-Cookie中，会以key-value
> 的形式返回给客户端
> 例：JSESSIONID=7F149950097E7B5B41B390436497CD21；其中 JSESSIONID 是固定的，
> 而后面的value值对应的则是给该客户端新创建的session的ID，之后浏览器再次进行服务
> 器访问的时候，客户端会将此key-value放到cookie中一并请求服务器，服务器就会根据
> 此ID寻找对应的session对象了

tomcat服务器的conf/web.xml文件中可以配置session的最大不活跃时间:
```xml
<session-config>
    <session-timeout>30</session-timeout>
</session-config>
```

___
**source code**
```java
package javax.servlet.http;

import java.util.Enumeration;
import javax.servlet.ServletContext;

/**
 * http会话
 * 当应用从session中存储或者移除对象时,session会检查这个对象
 * 是否实现了HttpSessionBindListener接口,如果实现了，那么
 * servlet会通知这个对象它被绑定或者取消绑定
 */
public interface HttpSession {

    /**
     * 返回session的创建时间(格林尼治起始时间1970.1.1后的毫秒数)
     */
    public long getCreationTime();
    
        
    /**
     * 返回由servlet容器生成的session唯一标识符字符串
     * tomcat生成随机数+时间+jvmid
     */
    public String getId();
    

    /**
     * 返回上一次客户端发送附带session的请求的时间(格林尼治起始时间1970.1.1后的毫秒数)
     * 此时间是容器记录
     */
    public long getLastAccessedTime();
    
    
    /**
     * 返回session属于的ServletContext
     */
    public ServletContext getServletContext();


    /**
     * 设置session失效时间, interval<=0则表示永不失效
     * 设置session失效时间有三种:
     * 1. web容器中tomcat-7.0/web.xml中在session-config标签中设置session-timeout标签
     * 2. 在工程的web.xml的session-config标签中设置session-timeout标签
     * 3. 代码设置session.setMaxInactiveInterval(秒数)
     */    
    public void setMaxInactiveInterval(int interval);


    /**
     * 获取最大时间间隔
     */
    public int getMaxInactiveInterval();
    

    /**
     * deprecated
     */
    @Deprecated
    public HttpSessionContext getSessionContext();
    

    /**
     * 返回session绑定的名字为name的对象，如果此名字下没有绑定，则返回null
     */
    public Object getAttribute(String name);
    
    
    /**
     * 此方法在2.2版本由getAttribute替代
     */
    @Deprecated
    public Object getValue(String name);
        

    /**
     * 返回绑定到此session的所有name集合
     */    
    public Enumeration<String> getAttributeNames();
    

    /**
     * 在2.2版本,此方法由getattributeNames替代
     */
    @Deprecated
    public String[] getValueNames();
    

    /**
     * 使用名字(不能是null)和对应的对象绑定在session,如果名字已经绑定，则对象被替换
     * 此方法执行会，如果新的对象实现了HttpSessionBindListener接口，则容器
     * 会调用HttpSessionBindingListener.valueBound方法, 容器然后通知web应用中
     * 所有的HttpSessionAttributeListener发生了属性变动
     *
     * 如果name之前有对象绑定，并且这个对象实现了HttpSessionBindingListener接口，那么
     * 会调用之前对象的HttpSessionBindingListener.valueUnbound方法  
     *
     * 如果value是null,则和removeAttribute方法效果一样，移除了之前的绑定 
     */
    public void setAttribute(String name, Object value);
    

    /**
     * 2.2版本,此方法由setAttribute替代
     */
    @Deprecated
    public void putValue(String name, Object value);


    /**
     * 移除session绑定的name对应的对象,如果没有绑定对象，那么什么都不做
     *
     * 如果此对象实现了HttpSessionBindListener接口，则容器
     * 会调用HttpSessionBindingListener.valueUnbound, 容器然后通知web应用中
     * 所有的HttpSessionAttributeListener发生了属性变动
     */
    public void removeAttribute(String name);


    /**
     * 2.2版本,此方法由removeAttribute替代
     */
    @Deprecated
    public void removeValue(String name);


    /**
     * 失效session并且解绑所有绑定的对象
     */
    public void invalidate();
    
    
    /**
     * 如果客户端还没有第一次收到sessionid或者不打算保持会话，那么返回true
     * 
     * 如果服务端使用基于cookie的session,但客户端禁用了cookie的使用，那么对于
     * 每次请求来说session都会是新的
     */
    public boolean isNew();
}
```