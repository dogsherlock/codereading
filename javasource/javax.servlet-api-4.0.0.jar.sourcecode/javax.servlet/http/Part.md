##### javax.servlet.http.Part
```java
package javax.servlet.http;

import java.io.*;
import java.util.*;

/**
 * 此类用于表示接受multipart/form-data MIME类型的POST请求的一部分或者表单项.
 */
public interface Part {
    /**
     * 获取用于提取part内容的输入流.
     * 
     * @throws IOException 从InputStream提取数据发生I/O错误
     */
    public InputStream getInputStream() throws IOException;

    /**
     * 获取part的MIME类型
     */
    public String getContentType();

    /**
     * 获取part的name
     * Content-Disposition: form-data; name="fieldName"; filename="filename.jpg"
     * 也就是Content-Disposition标头的filedName或表单字段名的字符串.
     */
    public String getName();

    /**
     * 获取客户端上传的文件名.
     */
    public String getSubmittedFileName();

    /**
     * 获取此部分的字节大小.
     */
    public long getSize();

    /**
     * 将上传的部分的内容写入磁盘.
     *
     * @throws IOException I/O Error
     */
    public void write(String fileName) throws IOException;

    /**
     * 删除part的数据和关联的临时目录.
     *
     * @throws IOException I/O Error
     */
    public void delete() throws IOException;

    /**
     * 获取指定name(不区分大小写)的标头. 如果part不包含指定name的标头，则返回null.
     * 如果有多个name的标头，则此方法返回part中的第一个.
     */
    public String getHeader(String name);

    /**
     * 获取part标头中指定name(不区分大小写)的所有值.
     *
     * 对返回集合的改变不影响此part
     */
    public Collection<String> getHeaders(String name);

    /**
     * 获取part中所有的标头name集合.
     *
     * 一些servlet容器不允许servlet通过此方法来访问标头，在
     * 这种情况下此方法返回null.
     */
    public Collection<String> getHeaderNames();
}
```