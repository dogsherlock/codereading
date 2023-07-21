##### javax.servlet.annotation.HttpConstraint
```java
package javax.servlet.annotation;

import java.lang.annotation.Documented;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import javax.servlet.annotation.ServletSecurity.EmptyRoleSemantic;
import javax.servlet.annotation.ServletSecurity.TransportGuarantee;

/**
 * @HttpConstraint注解没有@Target注解，表示可以用于任何元素上.
 * 此注解在ServletSecurity注解中使用，来表示所有方法被施加的安全约束(所有HTTP方法
 * 没有在ServletSecurity注解的注解类型元素HttpMethodConstraints中出现.)
 *
 * 有一个特殊的情况，@HttpConstraint返回的都是默认值，并且至少有一个HttpMethodConstraint
 * 返回的不全是默认值，@HttpConstraint表示不会在任何HTTP协议方法上施加安全约束.
 * 也就是@HttpConstraint的默认行为在此情况下不会覆盖HttpMethodConstraint指定的约束.
 */
@Documented
@Retention(RetentionPolicy.RUNTIME)
public @interface HttpConstraint {

    /**
     * 默认授权语义.
     * 当rolesAllowed返回非空数组的时候，此值是无意义的，此时不应该指定此值.
     *
     * @return 返回当rolesAllowed返回空数组(长度为0)，应用的EmptyRoleSemantic
     */
    EmptyRoleSemantic value() default EmptyRoleSemantic.PERMIT;

    /**
     * 客户端与服务端数据传输的保护要求(即，是否需要SSL/TSL安全协议加密)
     */
    TransportGuarantee transportGuarantee() default TransportGuarantee.NONE;

    /**
     * 授权角色名数组
     *
     * 出现在rolesAllowed中的重复角色名是无意义的，可能会在注解运行时处理时被丢弃.
     * 字符串"*"作为一个角色名没有特殊意义(是否可以出现在rolesAllowed中).
     *
     * @return 返回包含零个或多个角色名.当数组包含零个元素，含义取决于value注解类型元素
     * 的EmptyRoleSemantic值.如果此值为DENY，并且rolesAllowed为空数组，则访问被拒绝.
     * 如果此值为PERMIT，并且rolesAllowed为空数组，则访问被允许.如果数组含有元素，
     * 表示访问是由数组中角色来决定的(和value注解类型元素的EmptyRoleSemantic值无关).
     */
    String[] rolesAllowed() default {};
}
```