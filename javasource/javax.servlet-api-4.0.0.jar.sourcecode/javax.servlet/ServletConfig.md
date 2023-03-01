##### javax.servlet.ServletConfig
```java
package javax.servlet;

import java.util.Enumeration;

/**
 * servlet容器初始化servlet时通过servlet配置对象来传递信息
 */
public interface ServletConfig {
    
    /**
     * 返回servlet实例的name
     */
    public String getServletName();


    /**
     * 返回Servlet上下文,调用者用来与servlet容器交互
     */
    public ServletContext getServletContext();

    
    /**
     * 使用name来获得初始化参数的值,没有设置为null
     */
    public String getInitParameter(String name);


    /**
     * 返回servlet初始化参数的所有Enumeration<String>类型的name
     */
    public Enumeration<String> getInitParameterNames();
}
```
