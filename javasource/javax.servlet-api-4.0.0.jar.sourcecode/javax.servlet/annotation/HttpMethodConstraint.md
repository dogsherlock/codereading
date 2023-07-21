##### javax.servlet.annotation.HttpMethodConstraint
```java
package javax.servlet.annotation;

import java.lang.annotation.Documented;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import javax.servlet.annotation.ServletSecurity.EmptyRoleSemantic;
import javax.servlet.annotation.ServletSecurity.TransportGuarantee;

/**
 * 可以在ServletSecurity注解中使用此注解，表示特定的HTTP方法消息上的安全约束.
 */
@Documented
@Retention(RetentionPolicy.RUNTIME)
public @interface HttpMethodConstraint {

    /**
     * HTTP协议方法名.
     * 
     * @return 返回HTTP协议方法名，值可能不能为null、空字符串，并且必须是RFC 2616
     * 中定义的合法的HTTP方法名.
     */
    String value();

    /**
     * 默认授权语义.
     * 当rolesAllowed返回非空数组的时候，此值是无意义的，此时不应该指定此值.
     *
     * @return 返回当rolesAllowed返回空数组(长度为0)，应用的EmptyRoleSemantic
     */
    EmptyRoleSemantic emptyRoleSemantic() default EmptyRoleSemantic.PERMIT;

    /**
     * 客户端与服务端数据传输的保护要求(即，是否需要SSL/TSL安全协议加密)
     */
    TransportGuarantee transportGuarantee() default TransportGuarantee.NONE;

    /**
     * 授权角色名数组
     *
     * 出现在rolesAllowed中的重复角色名是无意义的，可能会在注解运行时处理时被丢弃.
     * 字符串"*"作为一个角色名没有特殊意义(是否可以出现在rolesAllowed中).
     */
    String[] rolesAllowed() default {};
}
```