##### javax.servlet.HttpConstraintElement
```java
package javax.servlet;

import java.util.*;
import javax.servlet.annotation.HttpConstraint;
import javax.servlet.annotation.ServletSecurity.EmptyRoleSemantic;
import javax.servlet.annotation.ServletSecurity.TransportGuarantee;

/**
 * 此类用于表示HttpConstraint注解值.
 */
public class HttpConstraintElement {

    /**
     * 定义应用于被允许的角色是空数组上的访问语义.
     */
    private EmptyRoleSemantic emptyRoleSemantic;

    /**
     * 定义数据传输的保护要求.
     */
    private TransportGuarantee transportGuarantee;

    /**
     * 授权的角色.
     */
    private String[] rolesAllowed;

    /**
     * 构造器 默认的HTTP约束元素
     * 也就是EmptyRoleSemantic.PERMIT、TransportGuarantee.NONE以及
     * rolesAllowed为空数组.
     */
    public HttpConstraintElement() {
        this(EmptyRoleSemantic.PERMIT);
    }

    /**
     * 便捷构造器 建立EmptyRoleSemantic.DENY
     */
    public HttpConstraintElement(EmptyRoleSemantic semantic) {
        this(semantic, TransportGuarantee.NONE, new String[0]);
    }

    /**
     * 构造器 建立非空返回值getRolesAllowed，和/或TransportGuarantee.CONFIDENTIAL
     */
    public HttpConstraintElement(TransportGuarantee guarantee,
            String... roleNames) {
        this(EmptyRoleSemantic.PERMIT, guarantee, roleNames);
    }

    /**
     * 构造器，建立getEmptyRoleSemantic/getRolesAllowed/getTransportGuarantee
     */
    public HttpConstraintElement(EmptyRoleSemantic semantic,
            TransportGuarantee guarantee, String... roleNames) {
        // roleNames非空，不应该指定EmptyRoleSemantic.DENY
        if (semantic == EmptyRoleSemantic.DENY && roleNames.length > 0) {
            throw new IllegalArgumentException(
                "Deny semantic with rolesAllowed");
        }
        this.emptyRoleSemantic = semantic;
        this.transportGuarantee = guarantee;
        this.rolesAllowed = copyStrings(roleNames);
    }

    /**
     * 获取默认授权语义.
     */
    public EmptyRoleSemantic getEmptyRoleSemantic() {
        return this.emptyRoleSemantic;
    }

    /**
     * 返回传输连接需要满足的数据保护要求(是否需要SSL/TSL安全协议).
     */
    public TransportGuarantee getTransportGuarantee() {
        return this.transportGuarantee;
    }

    /**
     * 返回授权角色名.
     */
    public String[] getRolesAllowed() {
        return copyStrings(this.rolesAllowed);
    }

    /**
     * 可以使用return (strings != null) ? strings.clone() : new String[0]
     * 达到相同的效果(深度拷贝字符串数组).
     */
    private String[] copyStrings(String[] strings) {
        String[] arr = null;
        if (strings != null) {
            int len = strings.length;
            arr = new String[len];
            if (len > 0) {
                System.arraycopy(strings, 0, arr, 0, len);
            }
        }

        return ((arr != null) ? arr : new String[0]);
    }
}
```