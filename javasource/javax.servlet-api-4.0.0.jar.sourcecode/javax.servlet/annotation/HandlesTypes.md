##### javax.servlet.annotation.HandlesTypes
```java
package javax.servlet.annotation;

import java.lang.annotation.Target;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;

/**
 * 此注解用来声明ServletContainerInitializer可以处理的类类型.
 */
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
public @interface HandlesTypes {

    /**
     * 为启动程序确定入参类型，详见javax.servlet.ServletContainerInitializer
     */
    Class<?>[] value();
}
```