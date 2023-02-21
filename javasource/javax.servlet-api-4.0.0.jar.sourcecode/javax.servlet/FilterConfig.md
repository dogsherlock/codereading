##### javax.servlet.FilterConfig
```java
package javax.servlet;

import java.util.Enumeration;

/** 
 * servlet容器初始化过滤器时将filter配置对象传递init方法中
 */
public interface FilterConfig {

    /** 
     * 返回过滤器的名字
     */
    public String getFilterName();


    /**
     * 返回ServletContext引用
     */
    public ServletContext getServletContext();
    

    /**
     * 返回name对应的初始化参数
     */
    public String getInitParameter(String name);


    /**
     * 返回初始化参数集合
     */
    public Enumeration<String> getInitParameterNames();
}
```
