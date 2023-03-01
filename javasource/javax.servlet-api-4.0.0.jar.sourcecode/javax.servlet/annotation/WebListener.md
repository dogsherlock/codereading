##### javax.servlet.annotation.WebListener
```java
package javax.servlet.annotation;

import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

/**
 * 此注解用于WebListener的声明
 * 可以用于类、接口、枚举，使用此注解的类需要实现以下任意接口
 * the {@link javax.servlet.ServletContextListener}, 
 * {@link javax.servlet.ServletContextAttributeListener},
 * {@link javax.servlet.ServletRequestListener},
 * {@link javax.servlet.ServletRequestAttributeListener}, 
 * {@link javax.servlet.http.HttpSessionListener}, or
 * {@link javax.servlet.http.HttpSessionAttributeListener}, or
 * {@link javax.servlet.http.HttpSessionIdListener} interfaces.
 * 
 */
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface WebListener {
    /**
     * 对此监听器的描述
     */
    String value() default "";
}
```