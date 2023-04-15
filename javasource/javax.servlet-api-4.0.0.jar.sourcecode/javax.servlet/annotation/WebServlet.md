##### javax.servlet.annotation.WebServlet
```java
package javax.servlet.annotation;

import java.lang.annotation.ElementType;
import java.lang.annotation.Target;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Retention;
import java.lang.annotation.Documented;

/**
 * 用来声明servlet的注解.
 *
 * 注解由容器部署时处理，对应的servlet在指定的URL模式下可用.
 */
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface WebServlet {
    
    /**
     * 指定servlet的name属性，等价于<servlet-name>
     */
    String name() default "";
    
    /**
     * 该属性等价于urlPatterns属性，两个属性不能同时使用.
     */
    String[] value() default {};

    /**
     * 指定一组servlet的URL匹配模式，等价于<url-pattern>标签.
     */
    String[] urlPatterns() default {};
    
    /**
     * 指定servlet的加载顺序，等价于<load-on-startup>标签.
     */
    int loadOnStartup() default -1;
    
    /**
     * 指定一组servlet初始化参数，等价于<init-param>标签.
     */
    WebInitParam [] initParams() default {};
    
    /**
     * 声明servlet是否支持异步操作模式，等价于<async-supported>标签.
     */
    boolean asyncSupported() default false;
    
    /**
     * servlet的小图标.
     */
    String smallIcon() default "";

    /**
     * servlet的大图标.
     */
    String largeIcon() default "";

    /**
     * servlet的描述信息，等价于<description>标签.
     */
    String description() default "";

    /**
     * servlet的显示名，通常配合工具使用，等价于<display-name>标签.
     */
    String displayName() default "";
}
```