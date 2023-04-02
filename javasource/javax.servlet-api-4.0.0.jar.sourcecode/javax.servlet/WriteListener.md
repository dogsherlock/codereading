##### javax.servlet.WriteListener
```java
package javax.servlet;

import java.io.IOException;0
import java.util.EventListener;

/**
 * 可以直接写入内容的回调通知机制
 */
public interface WriteListener extends EventListener {

    /**
     * 使用ServletOutputStream#setWriteListener方法注册WriterListener实例
     * 使用ServletOutputStream#isReady()方法返回false,当数据可以读取时，
     * 容器会调用此方法
     *
     * @throws IOException I/O Exception
     */
    public void onWritePossible() throws IOException;

    /**
     * 当使用非阻塞API写入数据发生错误调用此方法
     *
     * @param t throwable对象来说明写入操作失败原因
     */
    public void onError(final Throwable t);
}
```