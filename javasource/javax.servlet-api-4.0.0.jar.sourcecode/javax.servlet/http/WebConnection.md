##### javax.servlet.http.WebConnection
```java
package javax.servlet.http;

import java.io.IOException;
import javax.servlet.ServletInputStream;
import javax.servlet.ServletOutputStream;

/**
 * 此接口包装了一个被升级请求的连接. 允许协议处理器来对容器发送服务请求和
 * 状态查询.
 */
public interface WebConnection extends AutoCloseable {
    /**
     * 返回这个web连接的用于读取二进制数据的输入流.
     *
     * @exception IOException I/O错误
     */
    public ServletInputStream getInputStream() throws IOException;

    /**
     * 返回这个web连接的用于写入二进制数据的输出流.
     *
     * @exception IOException I/O错误
     */
    public ServletOutputStream getOutputStream() throws IOException;
}
```