#### jsp-config标签使用
**简介**
```
jsp-config被用来为web应用中的jsp文件提供全局配置信息.
它有两个子元素: <taglib>以及<jsp-property-group>.
<taglib>元素为web应用上被jsp页面使用的tag library提供信息.
<jsp-property-group>元素给一组jsp文件全局属性信息.
<taglib>在jsp 1.2就已经存在，<jsp-property-group>是jsp 2.0新增的元素.

<jsp-property-group>子元素:

1.<description>：设定的说明
2.<display-name>：设定名称
3.<url-pattern>：设定值所影响的范围，如：/CH2 或 /*.jsp
4.<el-ignored>：若为true，表示不支持EL 语法
5.<scripting-invalid>：若为true，表示不支持<% scripting %>语法
6.<page-encoding>：设定JSP 网页的编码
7.<include-prelude>：设置JSP 网页的抬头，扩展名为.jspf
8.<include-coda>：设置JSP 网页的结尾，扩展名为.jspf
```

**简单完整配置**
```xml
<jsp-config>
    <taglib>
        <taglib-uri>Taglib</taglib-uri>
        <taglib-location>/WEB-INF/tlds/MyTaglib.tld</taglib-location>
    </taglib>
    <jsp-property-group>
        <description>Special property group for JSP Configuration JSP example.</description>
        <display-name>JSPConfiguration</display-name>
        <url-pattern>/jsp/* </url-pattern>
        <el-ignored>true</el-ignored>
        <page-encoding>GB2312</page-encoding>
        <scripting-invalid>true</scripting-invalid>
        <include-prelude>/include/prelude.jspf</include-prelude>
        <include-coda>/include/coda.jspf</include-coda>
    </jsp-property-group>
</jsp-config>
```
___

#### javax.servlet.descriptor.JspConfigDescriptor
```java
package javax.servlet.descriptor;

import java.util.Collection;

/**
 * 此接口提供web应用的<jsp-config>相关配置的访问.
 *
 * 此配置是来源于web应用的web.xml和web-fragment.xml描述文件的聚合配置.
 *
 * @since Servlet 3.0
 */
public interface JspConfigDescriptor {

    /**
     * 获取由此JspConfigDescriptor表示的<jsp-config>元素的所有<taglib>子元素.
     *
     * 对返回集合的改变不影响此JspConfigDescriptor obj.
     */
    public Collection<TaglibDescriptor> getTaglibs();

    /**
     * 获取由此JspConfigDescriptor表示的<jsp-config>元素的所有
     * <jsp-property-group>子元素.
     */
    public Collection<JspPropertyGroupDescriptor> getJspPropertyGroups();
}
```