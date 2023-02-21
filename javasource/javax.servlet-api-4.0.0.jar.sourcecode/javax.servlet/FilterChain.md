##### javax.servlet.FilterChain
```java
package javax.servlet;

import java.io.IOException;

    /**
     * javax.servlet 包中提供了一个 FilterChain 接口，该接口由容器实现。
     * 容器将其实例对象作为参数传入 Filter 对象的 doFilter() 方法中。
     * Filter 对象可以使用 FilterChain 对象调用链中下一个 Filter 的 doFilter() 方法，
     * 若该 Filter 是链中最后一个过滤器，则调用目标资源的 service()方法
     */

public interface FilterChain {

    /**
     * 使用该方法可以调用过滤器链中的下一个 Filter 的 doFilter() 方法，若该 Filter 是
     * 链中最后一个过滤器，则调用目标资源的 service() 方法
     */
    public void doFilter(ServletRequest request, ServletResponse response) throws IOException, ServletException;
}
```