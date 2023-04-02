##### javax.servlet.AsyncListener
Servlet 3.0为异步处理提供了一个监听器，使用AsyncListener接口表示
此接口负责管理异步事件，它可以监控如下四种事件:
* 异步线程开始时，调用AsyncListener的onStartAsync(AsyncEvent event)方法
* 异步线程出错时，调用AsyncListener的onError(AsyncEvent event)方法
* 异步线程执行超时，则调用AsyncListener的onTimeout(AsyncEvent event)方法
* 异步执行完毕时，调用AsyncListener的onComplete(AsyncEvent event)方法

___

```java
package javax.servlet;

import java.io.IOException;
import java.util.EventListener;

/**
 * AsyncListener监听器会通知ServletRequest对象开始的异步事件，
 * 如异步线程开始时、异步线程超时时、异步线程完成时、异步线程出错时
 * 使用AsyncContext#addListener方法来添加一个AysncListener对象
 */
public interface AsyncListener extends EventListener {
    
    /**
     * 通知AsyncLitener监听器异步操作已经完成
     *
     * @throws IOException 处理AsyncEvent事件对象发生I/O相关错误
     */
    public void onComplete(AsyncEvent event) throws IOException;

    /**
     * 通知AsyncListener监听器异步操作超时
     *
     * @throws IOException 处理AsyncEvent事件对象发生I/O相关错误
     */
    public void onTimeout(AsyncEvent event) throws IOException;

    /**
     * 通知AsyncListener监听器异步操作执行失败
     *
     * @throws IOException 处理AsyncEvent事件对象发生I/O相关错误
     */
    public void onError(AsyncEvent event) throws IOException;

    /**
     * 通知AsyncListener监听器一个新的异步操作开始了，通过调用
     * ServletRequest#startAsync相关方法
     *
     * 可以通过AsyncEvent#getAsyncContext获取到异步操作对应的
     * AsyncContext对象
     * 
     * 除此之外，如果通过AsyncContext#addListener(AsyncListener,
     * ServletRequest, ServletResponse)来注册AsyncListener监听器，
     * 可以通过AsyncEvent#getSuppliedRequest和AsyncEvent#getSuppliedResponse
     * 获取传入的请求响应对象.
     *
     * AsyncEvent包含AsyncContext对象的引用，AsyncListener监听器对象只有通过
     * AsyncContext#addListener注册才可以接受事件生效
     *
     * @throws IOException 处理AsyncEvent事件对象发生I/O相关错误
     */
    public void onStartAsync(AsyncEvent event) throws IOException;     
}
```