##### javax.servlet.FilterRegistration
```java
package javax.servlet;

import java.util.*;

/**
 * 通过此接口可以进一步配置filter.
 */
public interface FilterRegistration extends Registration {

    /**
     * 添加FilterRegistration关联的Filter到Servlet的映射，
     * 使用Servlet Name、DispatcherType作为映射。isMatchAfter
     * 表示新添加的Mapping是在已有的Mapping之前还是之后
     *
     * 过滤器映射关系根据添加的顺序匹配.
     * 
     * 多次调用此方法，会影响之前的映射关系.
     *
     * @param dispatcherTypes 如果为null，则默认使用DispatcherType.REQUEST.
     * 
     * @param isMatchAfter 新添加的Mapping是在已有的Mapping之前还是之后
     * 
     * @param servletNames servlet names.
     *
     * @throws IllegalArgumentException 如果servletNames为null或者为空.
     * 
     * @throws IllegalStateException 如果ServletContext已经被初始化.
     */
    public void addMappingForServletNames(
        EnumSet<DispatcherType> dispatcherTypes, boolean isMatchAfter,
        String... servletNames);

    /**
     * 获取当前FilterRegistration关联的Filter已存在的servlet name的映射.
     */
    public Collection<String> getServletNameMappings();

    /**
     * 添加FilterRegistration关联的Filter到Servlet的映射，使用
     * URL patterns、DispatcherType作为映射.isMatchAfter表示
     * 新添加的Mapping是在已有的Mapping之前还是之后.
     *
     * 此方法与#addMappingForServletNames类似
     *
     * @throws IllegalArgumentException urlPatterns为null或者为空.
     *
     * @throws IllegalStateException 如果ServletContext已经被初始化.
     */
    public void addMappingForUrlPatterns(
        EnumSet<DispatcherType> dispatcherTypes, boolean isMatchAfter,
        String... urlPatterns);

    /**
     * 获取当前FilterRegistration关联的Filter已存在的url patterns的映射.
     */
    public Collection<String> getUrlPatternMappings();

    /**
     * 此接口用于在ServletContext上通过addFilter方法注册的filter进一步配置.
     */
    interface Dynamic extends FilterRegistration, Registration.Dynamic {
    }
}
```