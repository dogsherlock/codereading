##### javax.servlet.Servlet
```java
package javax.servlet;

import java.io.IOException;


/**
 * 所有servlets需要实现此接口
 * Defines methods that all servlets must implement.
 * 此接口定义了一些方法(生命周期方法)来初始化一个servlet、来响应请求、从服务器移除servlet
 * 这些方法根据如下顺序来调用:
 * <ol>
 * <li>servlet创建之后，调用init方法
 * <li>客户端的所有请求都会通过service方法来处理
 * <li> servlet从servlet容器中移除,然后由destory方法销毁，随后垃圾回收
 * </ol>
 * getServletConfig()方法用来获取servlet启动信息,getServletInfo()可以返回servlet的基本信息
 */


public interface Servlet {

    /**
     * servlet容器会在初始化servlet之后调用一次init()方法, 若过init()方法抛出ServletException
     * 异常或者在规定时间没有返回，那么无法放入servlet容器
     */
    public void init(ServletConfig config) throws ServletException;
    
    

    /**
     * 返回servlet的配置信息
     */
    public ServletConfig getServletConfig();
    
    

    /**
     * 由servlet容器主动调用来响应请求
     * servlets通常在多线程servlet容器中并发处理多个请求.开发者需要注意同步共享资源以及servlet类和实例变量
     */
    public void service(ServletRequest req, ServletResponse res)
	throws ServletException, IOException;
	
	

    /**
     * 返回servlet的纯文本信息，如作者、版本以及版权
     * Returns information about the servlet, such
     * as author, version, and copycopyrightright.
     */

    public String getServletInfo();
    
    

    /**
     * 由servlet容器调用来指示此servlet正被移出服务.
     */

    public void destroy();
}
```
