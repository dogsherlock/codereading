##### javax.servlet.AsyncEvent
```java
package javax.servlet;

/**
 * 当通过调用ServletRequest#startAsync初始化的异步操作开始、完成、超时、错误
 * 时触发此事件
 */
public class AsyncEvent { 

    private AsyncContext context;
    private ServletRequest request;
    private ServletResponse response;
    private Throwable throwable;

    /**
     * 用给定的AsyncContext对象构造一个AsyncEvent对象
     */
    public AsyncEvent(AsyncContext context) {
        this(context, context.getRequest(), context.getResponse(), null);
    }

    /**
     * 使用给定的AsyncContext、ServletRequest、ServletResponse对象构造
     * AsyncEvent事件对象
     */
    public AsyncEvent(AsyncContext context, ServletRequest request,
            ServletResponse response) {
        this(context, request, response, null);
    }

    /**
     * 使用给定的AsyncContext、Throwable对象构造AsyncEvent事件对象
     */
    public AsyncEvent(AsyncContext context, Throwable throwable) {
        this(context, context.getRequest(), context.getResponse(), throwable);
    }

    /**
     * 所有的构造函数最终都要流入此处
     */
    public AsyncEvent(AsyncContext context, ServletRequest request,
            ServletResponse response, Throwable throwable) {
        this.context = context;
        this.request = request;
        this.response = response;
        this.throwable = throwable;
    }

    /**
     * 获取AsyncContext obj
     */
    public AsyncContext getAsyncContext() {
        return context;
    }

    /**
     * 获取ServletRequest obj
     * Gets the ServletRequest from this AsyncEvent.
     *
     * 如果是通过AsyncContext#addListener(AsyncListener)注册AsyncListener监听器,
     * 那么此方法必须返回null
     */
    public ServletRequest getSuppliedRequest() {
        return request;
    }

    /**
     * 获取ServletResponse obj
     *
     * 类似AsyncEvent#getSuppliedRequest
     */
    public ServletResponse getSuppliedResponse() {
        return response;
    }

    /**
     * 获取Throwable obj
     */
    public Throwable getThrowable() {
        return throwable;
    }
}
```