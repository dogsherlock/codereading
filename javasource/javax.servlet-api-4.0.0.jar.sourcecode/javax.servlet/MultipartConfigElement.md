##### javax.servlet.MultipartConfigElement
```java
package javax.servlet;

import javax.servlet.annotation.MultipartConfig;

/**
 * 此类表示一个MultipartConfig注解值.
 */
public class MultipartConfigElement {

    /**
     * 指定上传文件的存储目录. 默认值是"".
     */
    private String location;
    
    /**
     * 单个上传文件的最大大小(bytes). 默认值是-1L.
     */
    private long maxFileSize;

    /**
     * 限制multipart/form-data请求中数据的大小(bytes). 默认值是-1L.
     */
    private long maxRequestSize;

    /**
     * 指定缓存大小，超过缓存大小会写入磁盘. 默认值是0.
     */
    private int fileSizeThreshold;

    /**
     * 构造器，使用给定的location参数，构造一个带有其他属性为默认值的实例.
     *
     * @param location location为null，则默认是"".
     */
    public MultipartConfigElement(String location) {
        if (location == null) {
            this.location = "";
        } else {
            this.location = location;
        }
        this.maxFileSize = -1L;
        this.maxRequestSize = -1L;
        this.fileSizeThreshold = 0;
    }

    /**
     * 构造器，所有的值由参数给定，构造实例.
     */
    public MultipartConfigElement(String location, long maxFileSize,
            long maxRequestSize, int fileSizeThreshold) {
        if (location == null) {
            this.location = "";
        } else {
            this.location = location;
        }
        this.maxFileSize = maxFileSize;
        this.maxRequestSize = maxRequestSize;
        this.fileSizeThreshold = fileSizeThreshold;
    }

    /**
     * 构造器，由参数MultipartConfig注解值创建实例.
     */
    public MultipartConfigElement(MultipartConfig annotation) {
        this.location = annotation.location();
        this.fileSizeThreshold = annotation.fileSizeThreshold();
        this.maxFileSize = annotation.maxFileSize();
        this.maxRequestSize = annotation.maxRequestSize();
    }

    /**
     * 获取存储文件的目录位置.
     */
    public String getLocation() {
        return this.location;
    }

    /**
     * 获取单个上传文件的最大大小(bytes)
     */
    public long getMaxFileSize() {
        return this.maxFileSize;
    }

    /**
     * 获取限制multipart/form-data请求中数据的大小(bytes).
     */
    public long getMaxRequestSize() {
        return this.maxRequestSize;
    }

    /**
     * 获取文件被写入磁盘的大小阈值.
     */
    public int getFileSizeThreshold() {
        return this.fileSizeThreshold;
    }
}
```