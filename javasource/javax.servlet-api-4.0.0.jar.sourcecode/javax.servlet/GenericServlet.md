##### javax.servlet.GenericServlet
> GenericServlet只有一个抽象方法service,继承该类，只需要重写service方法即可，
> 使用了适配器模式

```java
package javax.servlet;

import java.io.IOException;
import java.util.Enumeration;
import java.util.ResourceBundle;

/**
 * 定义了一个通用的、协议无关的servlet抽象类，如果要使用http servlet，
 * 则可以继承javax.servlet.http.HttpServlet
 * 如果要实现一个通用的servlet，你可以只覆写抽象方法service
 */
public abstract class GenericServlet 
    implements Servlet, ServletConfig, java.io.Serializable
{
    /* 可以使用ResourceBundle根据properties文件的名称信息(本地化信息)，
    匹配当前系统的国别语言信息(也可以第二个参数指定)，读取相应的properties文件
    内容
    文件名规则为自定义名_语言代码_国别代码.properties 
    */
    private static final String LSTRING_FILE = "javax.servlet.LocalStrings";
    private static ResourceBundle lStrings =
        ResourceBundle.getBundle(LSTRING_FILE);

    /* servlet's config obj */
    private transient ServletConfig config;

    /**
     * 无参构造器，servlet初始化由init方法完成
     */
    public GenericServlet() { }
    
    /**
     * 由servlet容器调用来表明servlet被移出服务(在整个
     * 生命周期中仅调用一次，以表明servlet正在被销毁)
     */
    public void destroy() {
    }
    
    /**
     * 返回servlet初始化参数name对应的value,如果name不存在返回null
     * 从servlet的ServletConfig属性返回name对应的value
     */ 
    public String getInitParameter(String name) {
        ServletConfig sc = getServletConfig();
        if (sc == null) {
            throw new IllegalStateException(
                // detail message: ServletConfig has not been initialized
                lStrings.getString("err.servlet_config_not_initialized"));
        }

        return sc.getInitParameter(name);
    }
    
   /**
    * 返回此servlet初始化参数的name集合
    */
    public Enumeration<String> getInitParameterNames() {
        ServletConfig sc = getServletConfig();
        if (sc == null) {
            throw new IllegalStateException(
                lStrings.getString("err.servlet_config_not_initialized"));
        }

        return sc.getInitParameterNames();
    }   
     
    /**
     * 返回此servlet的ServletConfig对象
     */    
    public ServletConfig getServletConfig() {
	return config;
    }
 
    /**
     * 返回Servlet上下文
     */
    public ServletContext getServletContext() {
        ServletConfig sc = getServletConfig();
        if (sc == null) {
            throw new IllegalStateException(
                lStrings.getString("err.servlet_config_not_initialized"));
        }

        return sc.getServletContext();
    }

    /**
     * 默认返回空字符串的servlet信息
     */    
    public String getServletInfo() {
	return "";
    }

    /**
     * 由servlet容器调用表明一个servlet被放入服务中
     */
    public void init(ServletConfig config) throws ServletException {
	this.config = config;
	this.init();
    }

    /**
     * 可以被覆写而无需调用super.init(config)方法
     */
    public void init() throws ServletException {

    }

    /**
     * 向servlet日志文件写入msg
     */     
    public void log(String msg) {
	getServletContext().log(getServletName() + ": "+ msg);
    }
   
    /**
     * 将解释性msg和堆栈信息写入servlet日志文件
     */   
    public void log(String message, Throwable t) {
	getServletContext().log(getServletName() + ": " + message, t);
    }
    
    /**
     * 由servlet容器主动调用来响应请求
     * 此方法被声明为抽象方法,所以子类如HttpServlet需要覆写实现它
     */
    public abstract void service(ServletRequest req, ServletResponse res)
	throws ServletException, IOException;
    
    /**
     * 返回servlet实例的name
     */
    public String getServletName() {
        ServletConfig sc = getServletConfig();
        if (sc == null) {
            throw new IllegalStateException(
                lStrings.getString("err.servlet_config_not_initialized"));
        }

        return sc.getServletName();
    }
}
```