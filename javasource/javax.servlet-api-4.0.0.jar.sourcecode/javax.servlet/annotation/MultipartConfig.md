##### javax.servlet.annotation.MultipartConfig
对于文件上传的实现，servlet提供了MultipartConfig注解和Part接口，
可以方便的处理HTTP请求中的文件上传.
1. form表单的enctype必须为multipart/form-data
2. 接受文件上传的servlet需要有@MultipartConfig注解
3. 从标头Content-Disposition中提取文件名.
4. 使用request.getPart(name)获取part
5. 使用part.write(path)进行文件持久化.

如下是web.xml(也可以直接使用MultipartConfig注解和@WebServlet注解)中的配置demo:
```xml
<servlet>
    <servlet-name>UploadServlet</servlet-name>
    <servlet-class>com.example.UploadServlet</servlet-class>
    <multipart-config>
        <max-file-size>1024</max-file-size>
        <max-request-size>1024</max-request-size>
        <file-size-threshold>0</file-size-threshold>
    </multipart-config>
</servlet>
<servlet-mapping>
    <servlet-name>UploadServlet</servlet-name>
    <url-pattern>/upload</url-pattern>
</servlet-mapping>
```

___

```java
package javax.servlet.annotation;

import java.lang.annotation.Target;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;

/**
 * 可能被用于javax.servlet.Servlet类上的注解，表示此Servlet实例用于
 * 使用multipart/form-data MIME类型的请求.
 *
 * 可以通过javax.servlet.http.HttpServletRequest#getPart或
 * javax.servlet.http.HttpServletRequest#getParts提取multipart/form-data
 * 请求的javax.servlet.http.Part部分.
 */
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface MultipartConfig {
    /**
     * 指定上传文件的存储目录.
     */
    String location() default "";

    /**
     * 单个上传文件的最大大小(bytes)，默认是-1L，表示没有限制
     */
    long maxFileSize() default -1L;

    /**
     * 限制multipart/form-data请求中数据的大小(bytes)，默认是-1L，表示没有限制.
     */
    long maxRequestSize() default -1L;

    /**
     * 指定缓存大小，超过缓存大小会写入磁盘.
     */
    int fileSizeThreshold() default 0;
}
```