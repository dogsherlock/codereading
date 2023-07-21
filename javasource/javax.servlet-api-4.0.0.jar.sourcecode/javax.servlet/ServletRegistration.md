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
     * 获取由ServletRegistration表示的Servlet的runAs信息.
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
         * 设置被应用于ServletRegistration定义的映射上的ServletSecurityElement.
         *
         * 此方法在ServletContext(从它获取的ServletRegistration)被初始化之前
         * 会应用到添加到ServletRegistration的所有映射.
         * 
         * 如果ServletRegistration的某个url pattern恰好通过ServletSecurity注解
         * 设置了安全约束，或者在之前调用过这个方法，那么此方法会替换这个url pattern
         * 的安全约束.
         * 
         * 如果某个url pattern没有通过ServletSecurity注解、<security-constraint>部署描述符、
         * 在之前调用此方法这些方式设置其安全约束，那么此方法通过参数ServletSecurityElement
         * 为其设置安全约束.
         *
         * 返回的集合不是来源于Dynamic obj，所以两者的相互改变都不会影响另一个.
         * 
         * @param constraint 传递被施加在此ServletRegistration映射的url patterns上的
         * 安全约束.
         * 
         * @return 已经通过<security-constraint>部署描述符建立安全约束的url patterns集合.
         * 此方法对返回集合的url patterns没有影响.
         * 
         * @throws IllegalArgumentException 如果constraint为null.
         * 
         * @throws IllegalStateException 如果ServletContext已经被初始化.
         */
        public Set<String> setServletSecurity(ServletSecurityElement constraint);

        /**
         * 设置由ServletRegistration定义的映射上被应用的MultipartConfigElement.
         * 如果多次调用此方法，则会覆盖前面的调用.
         *
         * @throws IllegalArgumentException multipartConfig为null.
         *
         * @throws IllegalStateException 如果ServletContext已经被初始化.
         */
        public void setMultipartConfig(
            MultipartConfigElement multipartConfig);

        /**
         * 为ServletRegistration设置runAs角色名.
         *
         * @throws IllegalArgumentException roleName为null.
         *
         * @throws IllegalStateException 如果ServletContext已经被初始化.
         */
        public void setRunAsRole(String roleName);
    }
}
```