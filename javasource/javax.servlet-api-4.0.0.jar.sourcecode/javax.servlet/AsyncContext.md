##### javax.servlet.AsyncContext
**AsyncContext提供了监听器模式添加事件AsyncListener**
* onComplete 异步请求处理完成触发 完成时需要调用asyncContext.complete()方法
* onTimeout 异步请求处理超时触发 一般来说采用异步请求的任务都是比较耗时的任务，
可以使用asyncContext.setTimeout设置异步任务的超时时间
* onError 异步处理错误的时候触发
* onStartAsync 异步处理开始的时候触发，即request.startAsync()

___

```java
package javax.servlet;

/**
 * 由ServletRequest对象初始化得到AsyncContext对象，此类表示异步操作的执行上下文
 *
 * AsyncContext对象由ServletRequest#startAsync()或ServletRequest#startAsync
 * (ServletRequest, ServletResponse)方法创建和初始化,重复调用这些方法将会返回
 * 相同的实例
 *
 * 发生异步操作超时事件，容器必须经过以下步骤: 
 * 在ServletRequest对象上初始化异步操作AsyncContext对象，所有注册在其上
 * 的AsyncListener实例都会调用AsyncListener#onTimeout方法
 * 如果沒有监听器调用#complete方法或者任何的#dispatch方法，那么出现带有状态码
 * 为HttpServletResponse.SC_INTERNAL_SERVER_ERROR(500)状态码的错误分发
 * 如果匹配不到错误的页面，或者错误页面没有调用#complete或任何#dispatch重载方法，
 * 则最终上层调用#complete
 */
public interface AsyncContext {

    /**
     * request对象的属性名，在此之下的原始URI对于#dispatch(String)或
     * #dispatch(ServletContext, String)是可用的
     */
    static final String ASYNC_REQUEST_URI = "javax.servlet.async.request_uri";

    /**
     * request对象的上下文路径属性，同上
     */
    static final String ASYNC_CONTEXT_PATH = "javax.servlet.async.context_path";

    /**
     * request对象的URL映射的运行时发现属性，同上
     */
    static final String ASYNC_MAPPING = "javax.servlet.async.mapping";

    /**
     * request对象的实际URL相对于请求的servlet的URL的路径属性，同上
     */
    static final String ASYNC_PATH_INFO = "javax.servlet.async.path_info";

    /**
     * request对象的ServletPath属性，同上
     */
    static final String ASYNC_SERVLET_PATH = "javax.servlet.async.servlet_path";

    /**
     * request对象的查询字符串属性，同上
     */
    static final String ASYNC_QUERY_STRING = "javax.servlet.async.query_string";

    /**
     * 通过ServletRequest#startAsync()或ServletRequest#startAsync(ServletRequest,
     * ServletResponse)方法初始化AsyncContext，获取此ServletRequest对象
     *
     * @exception IllegalStateException  如果#complete或#dispatch的相关方法在一个异步
     * 生命周期被调用过
     */
    public ServletRequest getRequest();

    /**
     * 同上，获取ServletResponse对象
     *
     * @exception IllegalStateException  同上
     */
    public ServletResponse getResponse();

    /**
     * 检查是否AsyncContext对象是用原始的或者应用包裹的request和response对象初始化的
     * 
     * 这个信息可能被用于在一个request对象被设置成异步模式之后，过滤器doFilter
     * 结束后是否保留request或repsonse包裹对象,或者释放
     *
     * @return 如果调用ServletRequest#startAsync，使用原始的request和response对象，
     * 始化AsyncContext；通过调用ServletRequest#startAsync(ServletRequest, ServletResponse)
     * 方法，ServletRequest和ServletResponse实例参数不携带任何应用提供的包装，
     * 则返回true,否则返回false.
     */
    public boolean hasOriginalRequestAndResponse();

    /**
     * 分发AsyncContext的request和response对象到servlet容器
     * 
     * 如果使用ServletRequest#startAsync(ServletRequest, ServletResponse)
     * 方法开启异步操作，并且传入的request参数是HttpServletRequest实例，
     * 则分发给HttpServletRequest#getRequestURI方法返回的URI，否则，分发给最后
     * 一次的请求的URL
     *
     * <p>如下的序列展示了是如何工作的:
     * <pre>{@code
     * // REQUEST dispatch to /url/A
     * AsyncContext ac = request.startAsync();
     * ...
     * ac.dispatch(); // ASYNC dispatch to /url/A
     * 
     * // REQUEST to /url/A
     * // FORWARD dispatch to /url/B
     * request.getRequestDispatcher("/url/B").forward(request,response);
     * // Start async operation from within the target of the FORWARD
     * // dispatch
     * ac = request.startAsync();
     * ...
     * ac.dispatch(); // ASYNC dispatch to /url/A
     * 
     * // REQUEST to /url/A
     * // FORWARD dispatch to /url/B
     * request.getRequestDispatcher("/url/B").forward(request,response);
     * // Start async operation from within the target of the FORWARD
     * // dispatch
     * ac = request.startAsync(request,response);
     * ...
     * ac.dispatch(); // ASYNC dispatch to /url/B
     * }</pre>
     * 
     * 此方法在向容器管理的线程传入request和response对象之后立即返回.
     * 在容器管理的线程之上进行分发操作. 如果容器调用startAsync方法初始分发操作
     * 返回之前调用此方法，则分发操作将会延迟，直到容器初始分发返回给容器.
     *
     * 请求对象的分发类型被设置为DispatcherType.ASYNC.不像
     * RequestDispatcher#forward(ServletRequest, ServletResponse)的
     * 向前分发，此response的缓冲区和请求头将不会被重置，如果请求已经被提交
     * ，再进行分发是合法的.
     *
     * 对于request和response的控制被委托给了分发的目标，当分发目标执行完成，则
     * response将会被关闭，除非调用了ServletRequest#startAsync()或
     * ServletRequest#startAsync(ServletRequest, ServletResponse)方法
     * 
     * 在执行此方法过程中出现的任何错误或者异常都需要被捕获并被容器处理，处理
     * 方式如下:
     * <ol>
     * <li>所有注册在AsyncContext对象的AsyncListener监听器的AsyncListener#onError
     * 方法都会被触发.并且可以通过AsyncEvent#getThrowable获取Throwable对象.</li>
     * <li>如果沒有监听器调用#complete方法或者任何的#dispatch方法，那么出现带
     * 有状态码为HttpServletResponse.SC_INTERNAL_SERVER_ERROR(500)状态码的
     * 错误分发，并且Throwable对象的detailMessage为request属性名为
     * ReqeustDispatcher.ERROR_EXCEPTION的值</li>
     * <li>如果匹配不到错误的页面，或者错误页面没有调用#complete或任何#dispatch
     * 重载方法，则最终上层调用#complete</li>
     *
     * 通过调用ServletRequest#startAsync方法来只能进行一个最多一个异步分发操作，
     * 任何在同样的异步操作中执行附加的异步分发操作都会导致IllegalStateException
     * 如果随后在分发后的请求中调用startAsync，则任何分发或者#complete方法可能会被
     * 调用
     *
     * @throws IllegalStateException 如果调用了#dispatch方法，但是没有在最终分发处
     * 调用startAsync方法；或者在调用#complete方法之后调用#dispatch方法
     */
    public void dispatch();

    /**
     * 将AsyncContext对象的request和response对象分发给给定的path，分发的path
     * 限定于AsyncContext对象初始化的ServletContext中
     *
     * 在AsyncContext实例初始化的ServletContext上下文中，path参数和
     * ServletRequest#getRequestDipatcher(String)参数相同
     *
     * 分发目标必须可以拿到所有路径相关的请求属性，原始的URI、上下文路径、
     * 路径信息、servlet路径、查询字符串必须可以通过request的对象的
     * 属性#ASYNC_REQUEST_URI、#ASYNC_CONTEXT_PATH、#ASYNC_PATH_INFO、
     * #ASYNC_SERVLET_PATH、#ASYNC_QUERY_STRING得到. 这些属性在重复分发中
     * 会反应原始路径的信息
     *
     * 通过调用ServletRequest#startAsync方法来只能进行一个最多一个异步分发操作，
     * 任何在同样的异步操作中执行附加的异步分发操作都会导致IllegalStateException
     * 如果随后在分发后的请求中调用startAsync，则任何分发或者#complete方法可能会被
     * 调用 
     *
     * @throws IllegalStateException 同#dispatch()
     */
    public void dispatch(String path);

    /**
     * 与#dispatch(String)区别给定了限定在ServletContext上的path
     *
     * @throws IllegalStateException 同#dispatch()
     */
    public void dispatch(ServletContext context, String path);

    /**
     * 结束从请求开始初始化的AsyncContext对象对应的异步操作，关闭用来
     * 初始化AsyncContext对象的resposne
     *
     * 任何注册的类型为AsyncLitener的监听器将会被触发AsyncListener#onComplet(AsyncEvent)
     * 方法
     *
     * 在调用ServletRequest#startAsync()或ServletRequest#startAsync
     * (ServletRequest, ServletResponse)之后或者调用#dispatch方法之前
     * 的任何时间调用此方法是合法的
     * 如果此方法是在容器初始分发调用startAsync方法返回容器之前调用，则
     * 不会生效，任何AsyncListener#onComplete(AsyncEvent)的调用将会延迟，
     * 知道容器初始分发返回给容器
     */
    public void complete();

    /**
     * 容器分发任务给一个线程，可能是线程池，来执行Runnable.容器可能传递适当的
     * 上下文信息给Runnable任务.如Catalina容器会调用此方法创建一个
     * "tomcat-util.jar\org\apache\tomcat\util\threads\TaskThread.class"线程
     */
    public void start(Runnable run);

    /**
     * 通过调用ServletRequest#startAsync相关方法开启异步操作，
     * 使用最近一次的AsyncContext实例的此方法注册监听器AsyncLitener
     *
     * 通过调用ServletRequest#startAsync初始化异步操作、异步操作完成、超时、
     * 错误，注册的AsyncLitener对象将会接收到AsyncEvent事件，通知监听器
     * 的顺序是添加监听器的顺序
     *
     * 如果使用ServletRequest#startAsync或ServletRequest#startAsync
     * (ServletRequest, ServletResponse)开启异步操作，通知监听器后，
     * 可以通过AsyncEvent对象获取相同的request和response对象
     *
     * @throws IllegalStateException 在分发过程中调用ServletRequest#startAsync
     */
    public void addListener(AsyncListener listener);

    /**
     * 可以通过触发的监听器的方法参数AsyncEvent对象获取此方法传入的
     * ServletRequest和ServletResponse对象
     * AsyncListener被注册后，request和response对象可能会做一些包装，不应该读取或者
     * 写入，可以用来释放关联的资源
     *
     * @throws IllegalStateException 同上
     */
    public void addListener(AsyncListener listener,
                            ServletRequest servletRequest,
                            ServletResponse servletResponse);

    /**
     * 使用传入的AsyncListener类实例化对象
     *
     * 返回的AsyncLitener实例在通过AsyncContext#addListener注册之前可能会
     * 进一步定制
     *
     * 传入的AsyncListener类对象需要有一个无参构造器，用来实例
     *
     * 如果传入的clazz表示managed bean，此方法支持资源注入
     * 
     * @throws ServletException clazz实例化失败
     */
    public <T extends AsyncListener> T createListener(Class<T> clazz)
        throws ServletException; 

    /**
     * 给AsyncContext设置超时时间(毫秒 <=0表示没有超时时间)
     *
     * 当#complete方法或者任何#dipatch方法被调用, timeout将会失效
     * 如果#setTimeout没有被调用,则使用容器的默认超时时间，可以通过
     * #getTimeout方法调用，默认是30000毫秒
     *
     * @throws IllegalStateException 在分发后ServletRequest#startAsync
     */
    public void setTimeout(long timeout);

    /**
     * 获取AsyncContext对象的timeout(毫秒)
     *
     * 此方法返回容器默认的异步操作timeout，或者最近一次通过#setTimeout方法
     * 设置的timeout，如果返回<=0的值，表示没有timeout
     */
    public long getTimeout();
}
```