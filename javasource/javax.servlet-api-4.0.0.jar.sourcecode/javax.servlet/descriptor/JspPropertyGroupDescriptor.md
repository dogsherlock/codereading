##### javax.servlet.descriptor.JspPropertyGroupDescriptor
```java
package javax.servlet.descriptor;

import java.util.Collection;

/**
 * 此接口提供对web应用的<jsp-property-group>相关配置的访问.
 *
 * 此配置是来源于web应用的web.xml和web-fragment.xml描述文件的聚合配置.
 *
 * @since Servlet 3.0
 */
public interface JspPropertyGroupDescriptor {
    
    /**
     * 获取由此JspPropertyGroupDescriptor表示的jsp属性组的url patterns.
     * <url-pattern>标签内容.
     */
    public Collection<String> getUrlPatterns();

    /**
     * 获取<el-ignored>标签内容，其指定EL表达式是否可以应用在由此
     * JspPropertyGroupDescriptor表示的jsp属性组映射的jsp页面.
     */
    public String getElIgnored();

    /**
     * 获取<page-encoding>标签内容，表示指定的jsp页面的默认编码格式.
     */
    public String getPageEncoding();

    /**
     * 获取<scripting-invalid>标签内容.此标签指定是否可以在jsp页面中使用
     * <% scripting %>语法.
     */
    public String getScriptingInvalid();

    /**
     * 获取<is-xml>标签内容，其指定jsp页面是否会被视作jsp文档(xml语法).
     */
    public String getIsXml();

    /**
     * 获取<include-prelude>标签内容.
     */
    public Collection<String> getIncludePreludes();

    /**
     * 获取<include-coda>标签内容.
     */
    public Collection<String> getIncludeCodas();

    /**
     * 获取<deferred-syntax-allowed-as-literal>标签内容.此标签指定字符序列
     * "#{"(通常被保留作为EL表达式)，如果在jsp页面中作为字符串字面值出现是否
     */
    public String getDeferredSyntaxAllowedAsLiteral();

    /**
     * 获取<trim-directive-whitespaces>标签内容.此标签指定当模板文本只含有
     * 空格时，是否应该从jsp页面的响应输出中移除.
     */
    public String getTrimDirectiveWhitespaces();

    /**
     * 获取<default-content-type>标签内容.此标签指定jsp页面的默认响应的content type.
     */
    public String getDefaultContentType();

    /**
     * 获取<buffer>标签内容.此标签指定了jsp页面的response缓冲区的默认大小.
     */
    public String getBuffer();

    /**
     * 获取<error-on-undeclared-namespace>标签内容.此标签指定了如果在jsp页面
     * 使用了带有未声明名字空间的标记，是否应该在翻译时间引发一个错误.
     */
    public String getErrorOnUndeclaredNamespace();
}
```