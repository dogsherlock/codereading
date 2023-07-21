##### javax.servlet.ServletSecurityElement
```java
package javax.servlet;

import java.util.*;
import javax.servlet.annotation.HttpMethodConstraint;
import javax.servlet.annotation.ServletSecurity;

/**
 * 此类用来表示ServletSecurity注解的值.
 */
public class ServletSecurityElement extends HttpConstraintElement {
    /**
     * 指定的HTTP方法的约束的方法名集合
     */
    private Collection<String> methodNames;

    /**
     * 指定的HTTP方法的约束集合.
     */
    private Collection<HttpMethodConstraintElement> methodConstraints;

    /**
     * 构造一个使用默认HttpConstraintElement值作为默认约束元素，并且没有
     * 特定于HTTP方法约束元素的实例.
     */
    public ServletSecurityElement() {
        methodConstraints = new HashSet<HttpMethodConstraintElement>();
        methodNames = Collections.emptySet();
    }

    /**
     * 构造一个使用指定HttpConstraintElement值作为约束元素，并且没有
     * 特定于HTTP方法约束元素的实例.
     */
    public ServletSecurityElement(HttpConstraintElement constraint) {
        super(constraint.getEmptyRoleSemantic(),
                constraint.getTransportGuarantee(),
                constraint.getRolesAllowed());
        methodConstraints = new HashSet<HttpMethodConstraintElement>();
        methodNames = Collections.emptySet();
    }

    /**
     * 构造使用默认HttpConstraintElement值作为默认约束元素，并带有指定方法约束的集合
     * 的实例.
     *
     * @throws IllegalArgumentException 如果方法名重复.
     */
    public ServletSecurityElement(
            Collection<HttpMethodConstraintElement> methodConstraints) {
        this.methodConstraints = (methodConstraints == null ?
            new HashSet<HttpMethodConstraintElement>() : methodConstraints);
        methodNames = checkMethodNames(this.methodConstraints);
    }

    /**
     * 使用指定的constraint(应用于所有http方法)和methodConstraints(应用于
     * 指定http方法约束的集合)参数构造实例.
     *
     * @throws IllegalArgumentException 如果方法名重复.
     */
    public ServletSecurityElement(HttpConstraintElement constraint,
            Collection<HttpMethodConstraintElement> methodConstraints) {
        super(constraint.getEmptyRoleSemantic(),
                constraint.getTransportGuarantee(),
                constraint.getRolesAllowed());
        this.methodConstraints = (methodConstraints == null ?
            new HashSet<HttpMethodConstraintElement>() : methodConstraints);
        methodNames = checkMethodNames(this.methodConstraints);
    }

    /**
     * 给定ServletSecurity注解值参数，使用构造器构造实例.
     *
     * @throws IllegalArgumentException 如果方法名重复.
     */
    public ServletSecurityElement(ServletSecurity annotation) {
        super(annotation.value().value(),
                annotation.value().transportGuarantee(),
                annotation.value().rolesAllowed());
        this.methodConstraints = new HashSet<HttpMethodConstraintElement>();
        for (HttpMethodConstraint constraint :
                annotation.httpMethodConstraints()) {
            this.methodConstraints.add(
                new HttpMethodConstraintElement(
                    constraint.value(),
                    new HttpConstraintElement(constraint.emptyRoleSemantic(),
                        constraint.transportGuarantee(),
                        constraint.rolesAllowed())));
        }
        methodNames = checkMethodNames(this.methodConstraints);
    }

    /**
     * 返回指定HTTP方法的约束元素的不可变集合(可能为空).
     *
     * 如果允许，改变返回的集合不能影响此ServletSecurityElement.
     */
    public Collection<HttpMethodConstraintElement> getHttpMethodConstraints() {
        return Collections.unmodifiableCollection(methodConstraints);
    }

    /**
     * 返回指定的HTTP方法的约束的方法名的不可变集合
     */
    public Collection<String> getMethodNames() {
        return Collections.unmodifiableCollection(methodNames);
    }

    /**
     * 检查methodConstraints中重复的方法名.
     *
     * @param methodConstraints
     *
     * @retrun 方法名集合.
     *
     * @throws IllegalArgumentException 如果方法名重复.
     */
    private Collection<String> checkMethodNames(
            Collection<HttpMethodConstraintElement> methodConstraints) {
        Collection<String> methodNames = new HashSet<String>();
        for (HttpMethodConstraintElement methodConstraint :
                        methodConstraints) {
            String methodName = methodConstraint.getMethodName();
            // HashSet#add(E e)返回false表示集合中元素重复，此时抛出异常.
            if (!methodNames.add(methodName)) {
                throw new IllegalArgumentException(
                    "Duplicate HTTP method name: " + methodName);
            }
        }
        return methodNames;
    }
}
```