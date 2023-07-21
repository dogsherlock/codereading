##### javax.servlet.HttpMethodConstraintElement
```java
package javax.servlet;

import javax.servlet.annotation.HttpMethodConstraint;

/**
 * 此类表示HttpMethodConstraint注解的值.
 */
public class HttpMethodConstraintElement extends HttpConstraintElement {

    // 方法名.
    private String methodName;

    /**
     * 构造器，构造一个带有默认HttpConstraintElement值的实例.
     */
    public HttpMethodConstraintElement(String methodName) {
        // 如果methodName为null或空，则为无效的方法名.
        if (methodName == null || methodName.length() == 0) {
            throw new IllegalArgumentException("invalid HTTP method name");
        }
        this.methodName = methodName;
    }

    /**
     * 构造一个指定HttpConstraintElement值的实例.
     */
    public HttpMethodConstraintElement(String methodName,
            HttpConstraintElement constraint) {
        super(constraint.getEmptyRoleSemantic(),
                constraint.getTransportGuarantee(),
                constraint.getRolesAllowed());
        if (methodName == null || methodName.length() == 0) {
            throw new IllegalArgumentException("invalid HTTP method name");
        }
        this.methodName = methodName;
    }

    /**
     * 获取HTTP方法名.
     */
    public String getMethodName() {
        return this.methodName;
    }
}
```