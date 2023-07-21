##### javax.servlet.ServletContext
**在web.xml中配置ServletContext初始化参数**
```xml
<!-- 下面创建了两个配置项，分别是name=clarence，age=18 
所有的servlet都可以访问全局参数
-->
<context-param>
	<param-name>name</param-name>
	<param-value>clarence</param-value>
</context-param>
 
<context-param>
	<param-name>age</param-name>
	<param-value>18</param-value>
</context-param>
```
___

```java
package javax.servlet;

import java.io.InputStream;
import java.net.MalformedURLException;
import java.net.URL;
import java.util.Enumeration;
import java.util.EnumSet;
import java.util.EventListener;
import java.util.Map;
import java.util.Set;
import javax.servlet.descriptor.JspConfigDescriptor;

/**
 * ServletContext定义了用以和servlet容器交流的一系列方法,如获取文件mime类型，分发请求以及
 * 写入文件日志
 * 每个web application都有一个ServletContext
 */
public interface ServletContext {

    /**
     * 临时文件目录
     */
    public static final String TEMPDIR = "javax.servlet.context.tempdir";

    /**
     * 包含了 web 应用的 WEB-INF/lib 目录中的 JAR 文件的名字列表
     */
    public static final String ORDERED_LIBS =
        "javax.servlet.context.orderedLibs";

    /**
     * 返回web应用的上下文path
     *
     */
    public String getContextPath();

    /**
     * 返回ServletContext对象对应的URL
     */
    public ServletContext getContext(String uripath);

    /**
     * 返回servlet容器支持的servlet api的最大版本(4)
     */
    public int getMajorVersion();

    /**
     * 返回servlet容器支持的servlet api的最小版本(0)
     */
    public int getMinorVersion();

    /**
     * 返回当前应用基于的Servlet规范最高版本
     */
    public int getEffectiveMajorVersion();

    /**
     * 返回当前应用基于的Servlet规范最低版本
     */
    public int getEffectiveMinorVersion();

    /**
     * 方法返回文件的MIME类型，未知类型为null, MIME类型是容器配置的。可用通过web.xml进行配置
     */
    public String getMimeType(String file);

    /**
     * 根据传入的路径，列出该路径下的所有资源路径
     */
    public Set<String> getResourcePaths(String path);

    /**
     * 指定路径的资源封装成URL实例并返回
     */
    public URL getResource(String path) throws MalformedURLException;

    /**
     * getResourceAsStream获取指定路径资源的输入流InputStream并返回
     */
    public InputStream getResourceAsStream(String path);

    /**
     * 将指定的资源(相对于上下文路径)包装成RequestDispatcher实例并返回
     */
    public RequestDispatcher getRequestDispatcher(String path);

    /**
     * 通过资源的名字来包装成RequestDispatcher实例并返回
     */
    public RequestDispatcher getNamedDispatcher(String name);

    /**
     * 总是返回null, 过时将被移除
     */
    @Deprecated
    public Servlet getServlet(String name) throws ServletException;

    /**
     * 总是返回空的enumeration, 保持二进制兼容，未来将被移除
     */
    @Deprecated
    public Enumeration<Servlet> getServlets();

    /**
     * 总是返回空的enumeration, 保持二进制兼容，未来将被移除
     */
    @Deprecated
    public Enumeration<String> getServletNames();

    /**
     * 向servlet日志文件写入msg, 日志文件类型和名字由servlet容器指定
     */
    public void log(String msg);

    /**
     * deprecated
     */
    @Deprecated
    public void log(Exception exception, String msg);

    /**
     * 将异常及错误信息写入日志文件
     */
    public void log(String message, Throwable throwable);

    /**
     * 根据资源虚拟路径，返回实际路径 如/index.html返回文件系统的真实路径
     */
    public String getRealPath(String path);

    /**
     * 获得servlet容器的名称和版本 格式如Apache Tomcat/7.0.47
     */
    public String getServerInfo();

    /**
     * 获取应用的初始化参数值(web.xml)
     */
    public String getInitParameter(String name);

    /**
     * 获取应用的初始化参数集合(web.xml)
     */
    public Enumeration<String> getInitParameterNames();

    /**
     * 设置应用初始化参数，如果存在返回false,如果不存在设置成功返回true
     */
    public boolean setInitParameter(String name, String value);

    /**
     * 返回给定name的servlet容器的属性,如果不存在则为null
     */
    public Object getAttribute(String name);

    /**
     * 返回此ServletContext的所有属性名集合
     */
    public Enumeration<String> getAttributeNames();

    /**
     * 设置属性名, 遵循java包名的风格
     */
    public void setAttribute(String name, Object object);

    /**
     * 通过name移除属性
     */
    public void removeAttribute(String name);

    /**
     * getServletContextName返回应用的名称，这里的名称是web.xml里面配置的display-name，如果没配置则* 返回null。
     */
    public String getServletContextName();

    /**
     * servlet容器中注入servlet方式, 效果和web.xml中配置以及使用WebService注解servlet是一样的,
     * 这种方式更加灵活，动态
     */
    public ServletRegistration.Dynamic addServlet(
        String servletName, String className);

    /**
     * 使用指定的servlet名在ServletContext注册servlet实例.
     * 
     * 可以通过返回的ServletRegistration.Dynamic obj进一步
     * 配置注册的servlet.
     * 
     * @throws IllegalStateException 如果此ServletContext已经被初始化.
     *
     * @throws UnsupportedOperationException 如果此ServletContext已经传递到
     * ServletContextListener#contextInitialized方法，其注册的监听器
     * ServletContextListener即没有在web.xml或web-fragment.xml中声明，
     * 也没有使用javax.servlet.annotation.WebListener注解.
     *
     * @throws IllegalArgumentException 如果给定的servlet实例实现了SingleThreadModel接口，
     * 或者参数servletName为null或为空.
     */
    public ServletRegistration.Dynamic addServlet(
        String servletName, Servlet servlet);

    /**
     * 同上
     */
    public ServletRegistration.Dynamic addServlet(String servletName,
        Class <? extends Servlet> servletClass);

    /**
     * Adds the servlet with the given jsp file to this servlet context.
     */
    public ServletRegistration.Dynamic addJspFile(
        String servletName, String jspFile);

    /**
     * 实例化给定的servlet类(必须定义一个无参构造器，或者默认)
     * 底层实现就是利用反射机制调用默认的无参构造函数进行实例化
     */
    public <T extends Servlet> T createServlet(Class<T> clazz)
        throws ServletException;

    /**
     * 根据servlet名称查找其注册信息，即ServletRegistration实例
     */
    public ServletRegistration getServletRegistration(String servletName);

    /**
     * 查询当前servlet上下文中所有的servlet的注册信息
     */
    public Map<String, ? extends ServletRegistration> getServletRegistrations();

    /**
     * addFilter/createFilter/getFilterRegistration/getFilterRegistrations都是提供
     * 编程的方式实现filter
     */
    public FilterRegistration.Dynamic addFilter(
        String filterName, String className);

    /**
     * 同上
     */
    public FilterRegistration.Dynamic addFilter(
        String filterName, Filter filter);

    /**
     * 同上
     */
    public FilterRegistration.Dynamic addFilter(String filterName,
        Class <? extends Filter> filterClass);

    /**
     * 同上
     */
    public <T extends Filter> T createFilter(Class<T> clazz)
        throws ServletException;

    /**
     * 同上
     */
    public FilterRegistration getFilterRegistration(String filterName);

    /**
     * 同上  获取ServletContext注册的filter对应的FilterRegistration Map
     */
    public Map<String, ? extends FilterRegistration> getFilterRegistrations();

    /**
     * getSessionCookieConfig方法返回SessionCookieConfig实例，这个实例可以用于获取和设置
     * 会话跟踪的cookie的属性。多次调用getSessionCookieConfig方法返回的SessionCookieConfig
     * 实例是同一个，说明SessionCookieConfig是单例的
     */
    public SessionCookieConfig getSessionCookieConfig();

    /**
     * 设置会话跟踪模式
     */
    public void setSessionTrackingModes(Set<SessionTrackingMode> sessionTrackingModes);

    /**
     * 获取默认的会话跟踪模式
     */
    public Set<SessionTrackingMode> getDefaultSessionTrackingModes();

    /**
     * 获取有效的会话跟踪模式
     */
    public Set<SessionTrackingMode> getEffectiveSessionTrackingModes();

    /**
     * 向ServletContext添加监听器，监听器需要实现下面的一个或多个接口
     * <ul>
     * <li>{@link ServletContextAttributeListener}
     * <li>{@link ServletRequestListener}
     * <li>{@link ServletRequestAttributeListener}
     * <li>{@link javax.servlet.http.HttpSessionAttributeListener}
     * <li>{@link javax.servlet.http.HttpSessionIdListener}
     * <li>{@link javax.servlet.http.HttpSessionListener}
     * </ul>
     */
    public void addListener(String className);

    /**
     * 同上
     */
    public <T extends EventListener> void addListener(T t);

    /**
     * 同上
     */
    public void addListener(Class <? extends EventListener> listenerClass);

    /**
     * 初始化clazz实例
     */
    public <T extends EventListener> T createListener(Class<T> clazz)
        throws ServletException;

    /**
     * 获取web.xml和web-fragment.xml中配置的<jsp-config>数据
     *
     * @throws UnsupportedOperationException 如果注册到ServletContext的ServletContextListener
     * 的#contextInitialized方法传递了此ServletContext对象，并且此监听器没有通过
     * web.xml或web-fragment.xml或使用WebListener注解声明.
     *
     * @since Servlet 3.0
     */
    public JspConfigDescriptor getJspConfigDescriptor();

    /**
     * 获取当前servlet上文的类加载器
     */
    public ClassLoader getClassLoader();

    /**
     * 申明安全角色
     */
    public void declareRoles(String... roleNames);

    /**
     * 方法返回servlet上下文（即应用）部署的逻辑主机名
     */
    public String getVirtualServerName();

    /**
     * 返回ServletContext默认支持的session超时时间
     */
    public int getSessionTimeout();

    /**
     * 为ServletContext设置session超时时间
     * @since Servlet 4.0
     */
    public void setSessionTimeout(int sessionTimeout);

    /**
     * 获取请求字符编码
     */
    public String getRequestCharacterEncoding();

    /**
     * 服务器默认接受iso-8859-1编码，可以使用此方法设置发往服务器的参数
     */
    public void setRequestCharacterEncoding(String encoding);

    /**
     * 获取响应字符编码
     */
    public String getResponseCharacterEncoding();

    /**
     * 设置响应字符编码
     */
    public void setResponseCharacterEncoding(String encoding);
}
```
