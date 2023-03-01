##### javax.servlet.Filter
> filter过滤器，对从客户端向服务端发送的请求进行过滤,也可以对服务端返回的响应进行处理.
> 应用场景有权限认证、敏感词过滤、压缩响应等.

**在web.xml中配置过滤器**
```xml
<filter>
    <!-- 过滤器名称 -->
    <filter-name>loginFilter</filter-name>
    <!-- 过滤器的包路径 -->
    <filter-class>com.youaresherlock.login.loginFilter</filter-class>
</filter>
<!-- 可选过滤器初始化参数 -->
<init-param>
    <param-name>参数名</param-name>
    <param-value>参数值</param-value>
<init-param>
<!-- 过滤器映射 -->
<filter-mapping>
    <filter-name>loginFilter</filter-name>
    <!-- 指定过滤器作用的对象 -->
    <url-pattern>*.jsp</url-pattern>
</filter-mapping>
<filter-mapping>
    <filter-name>loginFilter</filter-name>
    <url-pattern>*.do</url-pattern>
</filter-mapping>
```

___

**filter拦截流程**

* 当客户端发送请求后，在HttpServletRequest到达Servlet之前，Filter拦截客户端的HttpServletRequest
* 根据需要检查HttpServletRequest
* 在Filter调用doFilter对过滤放行。
* 到达Servlet后，处理请求，并产生HttpServletResponse响应给客户端
* 在HttpServletResponse到达客户端之前过滤拦截
* 根据需要对HttpServletResponse进行处理
* HttpServletResponse 到达客户端

___

**Filter.java**
```java
package javax.servlet;

import java.io.IOException;

/**
 * 过滤器filter可以在doFilter方法中对请求或者响应完成过滤操作，注册和映射
 * Filter有两种方式: 通过web.xml配置或@WebFilter注解配置
 */
public interface Filter {

    /** 
     * 由web容器调用实例化一个过滤器
     */
    default public void init(FilterConfig filterConfig) throws ServletException {}
	
	
    /**
     * FilterChain传递给这个方法可以让过滤器chain.doFilter()将请求和响应传递给下一个chain中的Filter
     */
    public void doFilter(ServletRequest request, ServletResponse response,
                         FilterChain chain)
            throws IOException, ServletException;


    /**
     * 由web容器调用来表明filter被移出服务,销毁filter
     */
    default public void destroy() {}
}
```
