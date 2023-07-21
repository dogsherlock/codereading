##### javax.servlet.descriptor.TaglibDescriptor
```java
package javax.servlet.descriptor;

/**
 * 此接口提供web应用的<taglib>相关配置的访问.
 * 
 * 此配置是来源于web应用的web.xml和web-fragment.xml描述文件的聚合配置.
 *
 * @since Servlet 3.0
 */
public interface TaglibDescriptor {
  
    /**
     * 获取由此TaglibDescriptor表示的tag library的统一资源定位符.
     * 也就是<taglib-uri>标签内容.
     */
    public String getTaglibURI();

    /**
     * 获取由此TaglibDescriptor表示的tag library的位置.
     * 也就是<taglib-location>标签内容.
     */    
    public String getTaglibLocation();    
}
```