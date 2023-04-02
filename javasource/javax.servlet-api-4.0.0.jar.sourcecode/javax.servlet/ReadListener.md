##### javax.servlet.ReadListener
```java
package javax.servlet;

import java.io.IOException;
import java.util.EventListener;

/**
 * 此类表示当HTTP请求数据可以直接被读取时的通知执行的一个回调机制
 */
public interface ReadListener extends EventListener {

    /**
     * 当一个ReadListener实例被注册在ServletInputStream对象上，当
     * ServletInputStream#isReady()方法返回false，当数据可以读取时，容器会调用此方法
     *
     * @throws IOException I/O Exception
     */
    public void onDataAvailable() throws IOException;

    /**
     * 当前请求的所有数据被读取时触发
     *
     * @throws IOException 同上
     */
    public void onAllDataRead() throws IOException;

    /**
     * 处理请求时发生错误时触发
     *
     * @param t throwable obj指示读取操作失败原因
     */
    public void onError(Throwable t);
}
```