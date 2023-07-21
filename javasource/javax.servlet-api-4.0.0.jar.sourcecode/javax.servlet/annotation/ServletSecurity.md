##### web安全验证机制

###### tomcat安全验证机制
具体配置可以查看`https://tomcat.apache.org/`的Documentation.

提供如下常见部署描述符:
```
<security-constraint>、<web-resource-collection>、
<auth-constraint>、<user-data-constraint>、<login-config>、<auth-method>
、<realm-name>、<form-login-config>、<security-role>、<role-name>.
```

限制某个资源的访问:
```xml
<!-- security-constraint允许不通过编程就可以限制对某个资源的访问 -->
<security-constraint>  
    <display-name>default</display-name>  
    <!-- 
        web-resource-collection标识需要限制访问的资源子集
        可以定义URL模式和HTTP方法，不存在HTTP方法，则将安全
        约束应用于所有的方法.
        <http-method>元素可以被赋予一个HTTP方法，如GET或POST.
    -->
    <web-resource-collection>  
        <!-- web-resource-name是与受保护资源相关联的名称. -->
        <web-resource-name>all files</web-resource-name>  
        <url-pattern>/*</url-pattern>  
    </web-resource-collection>  
    <!-- auth-constraint指定可以访问该资源集合的用户角色，如果没有指定，则应用于所有角色. -->
    <auth-constraint>  
        <!-- role-name元素包含安全角色的名称. -->
        <role-name>admin</role-name>  
    </auth-constraint>  
    <!-- 
        user-data-constraint用来显示如何保护在客户端和web容器之间传递的数据.
     -->
    <user-data-constraint>  
        <!-- 
            transport-guarantee有三个值:
            NONE，这意味着应用不需要传输保证
            INTEGRAL，意味着服务器和客户端之间的数据
            必须以某种方式发送，而且在传送中不能改变
            CONFIDENTIAL，这意味着传输的数据必须是加密的数据
        -->
        <transport-guarantee>NONE</transport-guarantee>  
    </user-data-constraint>  
</security-constraint>  
```

提供四种类型的安全认证:
```xml
  <!-- 四种认证类型 -->
    <!-- BASIC：HTTP规范，Base64 这种方式被认为是最不安全的认证，因为它没有提供强烈的加密措施 -->
    <login-config>
        <auth-method>BASIC</auth-method>
    </login-config>
    <!-- DIGEST：HTTP规范，数据完整性强一些，但不是SSL 相比于BASIC认证，它是种比较安全的认证，它在认证时将请求数据 通过MD5的加密方式进行认证 -->
    <login-config>
        <auth-method>DIGEST</auth-method>
    </login-config>
    <!-- CLIENT-CERT：J2EE规范，数据完整性很强，公共钥匙(PKC) 这是一种基于客户端证书的认证方式，比较安全。但缺陷是在没有安全证书的客户端无法使用 -->
    <login-config>
        <auth-method>CLIENT-CERT</auth-method>
    </login-config>
    <!-- FORM：J2EE规范，数据完整性非常弱，没有加密，允许有定制的登录界面 这是种基础自定义表单的认证，你可以指定登录时的验证表单 -->
    <login-config>
        <auth-method>FORM</auth-method>
        <form-login-config>
            <form-login-page>/login.html</form-login-page>
            <form-error-page>/error.jsp</form-error-page>
        </form-login-config>
    </login-config>
```

security-role元素指定用于安全约束中的安全角色的声明:
```xml
<security-role>
    <description>admin</description>
    <role-name>admin</role-name>
</security-role>
```

___

##### javax.servlet.annotation.ServletSecurity
```java
package javax.servlet.annotation;

import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Inherited;
import java.lang.annotation.Target;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;

/**
 * 此注解用于servlet实现类，指定servlet容器在http协议消息上实施的安全约束.
 * servlet容器将会在使用此注解的servlets上实施约束(也就是servlets映射的urlpatterns上).
 */
@Inherited
@Documented
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface ServletSecurity {

    /**
     * 定义应用于被允许的角色是空数组上的访问语义.
     * 也就是等同于tomcat <auth-constraint>的未指定默认行为.
     */
    enum EmptyRoleSemantic {
        /**
         * 访问将会被允许，独立于验证状态和角色.
         */
        PERMIT,

        /**
         * 访问将会被拒绝，独立于验证状态和角色.
         */
        DENY
    }

    /**
     * 定义数据传输的保护要求.
     * 等同tomcat <user-data-constraint>部署描述符.
     */
    enum TransportGuarantee {
        /**
         * 不需要保护用户数据.
         * no protection of user data must be performed by the transport.
         */
        NONE,

        /**
         * 所有的用户数据传输需要通过安全协议进行加密(通常使用SSL/TLS).
         */
        CONFIDENTIAL
    }

    /**
     * 返回施加在所有HTTP方法(httpMethodConstraints返回中没有对应此方法)
     * 上的定义保护措施的HttpConstraint.
     */
    HttpConstraint value() default @HttpConstraint;

    /**
     * 获取指定的HTTP方法的约束，每个HttpMethodConstraint有一个HTTP协议方法
     * 以及定义了施加在此方法上的保护措施.
     *
     * 对于任何HTTP方法名，在返回的数组中至少有一个对应元素.
     * 如果返回的数组长度为0，则表明没有定义HTTP方法指定的约束.
     */
    HttpMethodConstraint[] httpMethodConstraints() default {};
}
```