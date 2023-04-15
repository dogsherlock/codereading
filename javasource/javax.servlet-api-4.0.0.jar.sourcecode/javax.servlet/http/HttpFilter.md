##### javax.servlet.http.HttpFilter
```java
package javax.servlet.http;

import java.io.IOException;
import javax.servlet.FilterChain;
import javax.servlet.GenericFilter;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;

/**
 * 提供一个用于子类的抽象类，为web网站创建一个合适的HTTP过滤器.
 * HttpFilter的子类应该重写#doFilter(HttpServletRequest，
 * HttpServletResponse，FilterChain)方法.
 * 
 * 过滤器通常运行在多线程服务器上，因此过滤器需要处理并发请求，
 * 以及小心处理访问共享资源的同步问题.共享资源包括内存数据如实例或者
 * 类变量以及外部对象如文件、数据库连接、以及网络连接.可以参考 
 * <a href="https://docs.oracle.com/javase/tutorial/essential/concurrency/">
 * Java多线程编程教程</a> 获取更多关于如何在Java程序中处理多线程的信息.
 */
public abstract class HttpFilter extends GenericFilter
{
    
    /**
     * 无参构造器.
     */
    public HttpFilter() { }

    /**
     * 由于客户端请求的资源在调用链的末尾，当resquest/response传送到调用链，
     * 容器都会调用过滤器的doFilter方法.
     * 传入此方法的参数FilterChain允许过滤器将request/response传到调用链下一个实体.
     * 
     * 默认实现检测传入的req和res对象是否分别是HttpServletRequest和
     * HttpServletResponse的实例.如果不是，抛出ServletException异常.
     * 否则，调用protected方法#doFilterdoFilter(HttpServletRequest req, 
     * HttpServletResponse res, FilterChain chain).
     * 此方法无需被重写.
     * 
     * @throws IOException 过滤器处理请求发生I/O Error
     *
     * @throws ServletException 无法处理请求，参数实例分别不是HttpServletRequest
     * 以及HttpServletResponse
     */
    @Override
    public void doFilter(ServletRequest req, ServletResponse res, FilterChain chain) throws IOException, ServletException {
        if (!(req instanceof HttpServletRequest &&
                res instanceof HttpServletResponse)) {
            throw new ServletException("non-HTTP request or response");
        }

        this.doFilter((HttpServletRequest)req, (HttpServletResponse)res, chain);
    }
    
    /**
     * 类似#doFilter(ServletRequest req, ServletResponse res, FilterChain chain).
     * 
     * 默认实现是简单的调用FilterChain#doFilter.
     *
     * @throws IOException 过滤器处理请求发生I/O Error
     *
     * @throws ServletException  无法处理请求
     */
    protected void doFilter(HttpServletRequest req, HttpServletResponse res, FilterChain chain) throws IOException, ServletException {
        chain.doFilter(req, res);
    }
}
```