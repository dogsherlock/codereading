##### javax.servlet.GenericFilter
```java
package javax.servlet;

import java.io.IOException;
import java.util.Enumeration;
import java.util.ResourceBundle;

/**
 * 定义一个通用的、协议独立的过滤器抽象类.要写一个用于web的
 * HTTP过滤器，可以继承javax.servlet.http.HttpFilter.
 * GenericFilter实现了Filter和FilterConfig接口，可以直接在此类上扩展，不过通常
 * 是继承一个特定协议的子类如HttpFilter.
 *
 * GenericFilter使实现过滤器变得简单.它提供了简单版本的生命周期方法init和
 * destory方法，还有FilterConfig中的方法.
 *
 * 要实现一个通用的过滤器，你只需要重写doFilter抽象方法.
 */
public abstract class GenericFilter 
    implements Filter, FilterConfig, java.io.Serializable
{
    private static final String LSTRING_FILE = "javax.servlet.LocalStrings";

    private static final ResourceBundle lStrings =
        ResourceBundle.getBundle(LSTRING_FILE);

    /**
     * 不会序列化FilterConfig obj配置信息
     */
    private transient FilterConfig config;

    /**
     * 无参构造器，所有过滤器的初始化由init系列方法完成.
     */
    public GenericFilter() { }
    
    /**
     * 返回指定name的初始化参数值，如果不存在，返回null.
     */ 
    @Override
    public String getInitParameter(String name) {
        FilterConfig fc = getFilterConfig();
        if (fc == null) {
            throw new IllegalStateException(
                lStrings.getString("err.filter_config_not_initialized"));
        }

        return fc.getInitParameter(name);
    }
    
   /**
    * 返回过滤器初始化参数名集合，如果没有，则返回空的集合.
    */
    @Override
    public Enumeration<String> getInitParameterNames() {
        FilterConfig fc = getFilterConfig();
        if (fc == null) {
            throw new IllegalStateException(
                lStrings.getString("err.filter_config_not_initialized"));
        }

        return fc.getInitParameterNames();
    }   
     
    /**
     * 返回servlet的ServletConfig obj.
     */    
    public FilterConfig getFilterConfig() {
	    return config;
    }
    
    /**
     * 返回此过滤器运行在的ServletContext obj的引用.
     */
    @Override
    public ServletContext getServletContext() {
        FilterConfig sc = getFilterConfig();
        if (sc == null) {
            throw new IllegalStateException(
                lStrings.getString("err.filter_config_not_initialized"));
        }

        return sc.getServletContext();
    }

    /**
     * 由容器调用表明将过滤器放到服务中.
     * 
     * 此方法的实现存储从servlet容器接受FilterConfig对象便于以后使用.
     * 当重写此方法内容时，要调用super.init(config).
     *
     * @exception ServletException 发生异常中断了servlet正常执行.
     */
    @Override
    public void init(FilterConfig config) throws ServletException {
        this.config = config;
        this.init();
    }

    /**
     * 一个便于重写的方法，不需要调用super.init(config).
     * 简单重写此方法而不是重写#init(FilterConfig)方法，将会由
     * GenericFilter.init(FilterConfig config)调用.可以通过
     * #getFilterConfig获取FilterConfig obj.
     */
    public void init() throws ServletException {

    }
    
    /**
     * 返会过滤器实例的name.
     */
    @Override
    public String getFilterName() {
        FilterConfig sc = getFilterConfig();
        if (sc == null) {
            throw new IllegalStateException(
                lStrings.getString("err.servlet_config_not_initialized"));
        }

        return sc.getFilterName();
    }
}
```