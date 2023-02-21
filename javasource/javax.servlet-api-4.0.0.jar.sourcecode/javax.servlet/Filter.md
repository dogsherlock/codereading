##### javax.servlet.Filter
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
