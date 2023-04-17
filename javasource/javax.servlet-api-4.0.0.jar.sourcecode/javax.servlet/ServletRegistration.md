##### javax.servlet.ServletRegistration
```java
package javax.servlet;

import java.util.*;

/**
 * 通过此接口可以进一步配置servlet.
 */
public interface ServletRegistration extends Registration {

    /**
     * 使用给定的url patterns参数向由ServletRegistration表示的Servlet加入
     * servlet映射信息.
     *
     * 如果任何给定的url patterns已经映射到了其它的servlet，那么将不会更新.
     *
     * @param urlPatterns servlet mapping的url patterns信息.
     *
     * @return 已经映射到其它的servlet的url patterns(可能为空).
     *
     * @throws IllegalArgumentException urlPatterns为空或者null.
     * 
     * @throws IllegalStateException 如果ServletContext已经被初始化.
     */
    public Set<String> addMapping(String... urlPatterns);

    /**
     * 获取由ServletRegistration表示的Servlet当前可用的映射信息.
     */
    public Collection<String> getMappings();

    /**
     * 获取由ServletRegistration表示的Servlet的runAs role信息.
     */
    public String getRunAsRole();

    /**
     * 此接口用于在ServletContext上通过addServlet方法注册的servlet进一步配置.
     */
    interface Dynamic extends ServletRegistration, Registration.Dynamic {

        /**
         * 设置由此动态ServletRegistration表示的Servlet的启动时加载的优先级.
         * 当loadOnStartup>=0，表明容器应当在ServletContext初始化阶段实例化
         * 和初始化此servlet.也就是说，在此之后，所有注册在ServletContext的
         * ServletContextListener对象将会触发其#contextInitialized方法.
         *
         * 当loadOnStartup<0，容器会惰性实例化以及初始化servlet.
         *
         * loadOnStartup默认为-1.
         * 
         * 调用此方法将会覆盖以前的设置.
         *
         * @param loadOnStartup servlet的初始化优先级.
         *
         * @throws IllegalStateException 如果ServletContext已经被初始化.
         */
        public void setLoadOnStartup(int loadOnStartup);

        /**
         * Sets the {@link ServletSecurityElement} to be applied to the
         * mappings defined for this <code>ServletRegistration</code>.
         *
         * <p>This method applies to all mappings added to this
         * <code>ServletRegistration</code> up until the point that the
         * <code>ServletContext</code> from which it was obtained has been
         * initialized.
         * 
         * <p>If a URL pattern of this ServletRegistration is an exact target
         * of a <code>security-constraint</code> that was established via
         * the portable deployment descriptor, then this method does not
         * change the <code>security-constraint</code> for that pattern,
         * and the pattern will be included in the return value.
         * 
         * <p>If a URL pattern of this ServletRegistration is an exact
         * target of a security constraint that was established via the
         * {@link javax.servlet.annotation.ServletSecurity} annotation
         * or a previous call to this method, then this method replaces
         * the security constraint for that pattern.
         * 
         * <p>If a URL pattern of this ServletRegistration is neither the
         * exact target of a security constraint that was established via
         * the {@link javax.servlet.annotation.ServletSecurity} annotation
         * or a previous call to this method, nor the exact target of a
         * <code>security-constraint</code> in the portable deployment
         * descriptor, then this method establishes the security constraint
         * for that pattern from the argument
         * <code>ServletSecurityElement</code>.
         *
         * <p>The returned set is not backed by the {@code Dynamic} object,
         * so changes in the returned set are not reflected in the
         * {@code Dynamic} object, and vice-versa.</p>
         * 
         * @param constraint the {@link ServletSecurityElement} to be applied
         * to the patterns mapped to this ServletRegistration
         * 
         * @return the (possibly empty) Set of URL patterns that were already
         * the exact target of a <code>security-constraint</code> that was
         * established via the portable deployment descriptor. This method
         * has no effect on the patterns included in the returned set
         * 
         * @throws IllegalArgumentException if <tt>constraint</tt> is null
         * 
         * @throws IllegalStateException if the {@link ServletContext} from
         * which this <code>ServletRegistration</code> was obtained has
         * already been initialized 
         */
        public Set<String> setServletSecurity(ServletSecurityElement constraint);

        /**
         * Sets the {@link MultipartConfigElement} to be applied to the
         * mappings defined for this <code>ServletRegistration</code>. If this
         * method is called multiple times, each successive call overrides the
         * effects of the former.
         *
         * @param multipartConfig the {@link MultipartConfigElement} to be
         * applied to the patterns mapped to the registration
         *
         * @throws IllegalArgumentException if <tt>multipartConfig</tt> is
         * null
         *
         * @throws IllegalStateException if the {@link ServletContext} from
         * which this ServletRegistration was obtained has already been
         * initialized
         */
        public void setMultipartConfig(
            MultipartConfigElement multipartConfig);

        /**
         * Sets the name of the <code>runAs</code> role for this
         * <code>ServletRegistration</code>.
         *
         * @param roleName the name of the <code>runAs</code> role
         *
         * @throws IllegalArgumentException if <tt>roleName</tt> is null
         *
         * @throws IllegalStateException if the {@link ServletContext} from
         * which this ServletRegistration was obtained has already been
         * initialized
         */
        public void setRunAsRole(String roleName);

    }
}
```