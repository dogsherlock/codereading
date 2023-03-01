##### javax.servlet.annotation.WebFilter
```java
package javax.servlet.annotation;

import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import javax.servlet.DispatcherType;

/**
 * 用于声明一个servlet过滤器的注解, 容器会在部署时根据具体的属性配置将
 * 相应的类部署为过滤器
 */
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface WebFilter {

    /**
     * 过滤器的描述,相当于<description>标签
     */
    String description() default "";
    
    /**
     * 该过滤器的显示名，通常配合工具使用,相当于<display-name>标签
     */
    String displayName() default "";
    
    /**
     * 过滤器初始化参数，相当于<init-param>标签
     */
    WebInitParam[] initParams() default {};
    
    /**
     * 过滤器的名字，相当于<filter-name>
     */
    String filterName() default "";
    
    /**
     * 指定过滤器的small icon
     */
    String smallIcon() default "";

    /**
     * 指定过滤器的large icon
     */
    String largeIcon() default "";

    /**
     * 指定过滤器应用于哪些过滤器上,取值是@WebServlet中name属性的值
     * 或者是web.xml中的<servlet-name>的取值
     */
    String[] servletNames() default {};
    
    /**
     * value属性等价于urlPatterns属性,但两者不应该同时使用, 
     * 默认返回空数组
     */
    String[] value() default {};

    /**
     * 指定一组过滤器的URL匹配模式，相当于<url-pattern>标签
     */
    String[] urlPatterns() default {};

    /**
     * 指定过滤器的转发模式,具体取值包括: 
     * FORWARD、INCLUDE、REQUEST、ASYNC、ERROR, 默认是DispatcherType.REQUEST
     */
    DispatcherType[] dispatcherTypes() default {DispatcherType.REQUEST};
    
    /**
     * 声明过滤器是否支持异步操作模式，等价于<async-supported>标签，默认是false
     */
    boolean asyncSupported() default false;
}
```