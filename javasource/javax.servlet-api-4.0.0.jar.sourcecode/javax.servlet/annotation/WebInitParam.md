##### javax.servlet.annotation.WebInitParam
```java
package javax.servlet.annotation;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import java.lang.annotation.Documented;

/**
 * @WebInitParam注解用于在servlet生成时初始化一些自定义的特定参数或者过滤器的实现类的参数初始化
 * 如@WebServlet(value = "myservlet",
 *      initParams = {
 *               @WebInitParam(name = "test", value = "testValue"),
 *               @WebInitParam(name = "go", value = "goValue")})
 *   public class MyServlet extends HttpServlet { ... }
 */
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface WebInitParam {

    /**
     * 初始化参数名
     */
    String name();
    
    /**
     * 初始化参数值
     */    
    String value();
    
    /**
     * 初始化参数的描述
     */
    String description() default "";
}
```