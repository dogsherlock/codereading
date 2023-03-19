##### javax.servlet.ServletRequest
**servlet异步请求**

在servlet3.0之前，一个普通的servlet的主要工作流程大致如下:
* servlet接受到请求之后，可能需要队请求携带的数据进行一些预处理
* 调用业务接口的某些方法，以完成业务处理
* 根据处理的结果提交响应,servelt线程结束

第二部处理业务逻辑的时候可能碰到比较耗时的任务，此时servlet主线程会阻塞
等待完成业务处理，对于并发比较大的请求可能会产生性能瓶颈.

servlet3.0之后支持异步操作，可以在调用业务接口的方法过程程中使用request.startAsycn方法，
获取一个AsyncContext，然后本servlet被回收到线程池，而AsyncContext来处理并响应

___

**使用asyncContext.start处理异步请求**
```java
package com.youaresherlock.demo1;

import jakarta.servlet.AsyncContext;
import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;
import java.util.concurrent.TimeUnit;

//1.设置@WebServlet的asyncSupported属性为true，表示支持异步处理
@WebServlet(name = "AsyncServlet1",
        urlPatterns = "/asyncServlet1",
        asyncSupported = true
)
public class AsyncServlet1 extends HttpServlet {
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        long st = System.currentTimeMillis();
        System.out.println("主线程：" + Thread.currentThread() + "-" + System.currentTimeMillis() + "-start");
        //2、启动异步处理：调用req.startAsync(request,response)方法，获取异步处理上下文对象AsyncContext
        AsyncContext asyncContext = request.startAsync(request, response);
        //3、调用start方法异步处理，调用这个方法之后主线程就结束了
        asyncContext.start(() -> {
            long stSon = System.currentTimeMillis();
            System.out.println("子线程：" + Thread.currentThread() + "-" + System.currentTimeMillis() + "-start");
            try {
                //这里休眠2秒，模拟业务耗时
                TimeUnit.SECONDS.sleep(2);
                //这里是子线程，请求在这里被处理了
                asyncContext.getResponse().getWriter().write("ok");
                //4、调用complete()方法，表示请求请求处理完成
                asyncContext.complete();
            } catch (Exception e) {
                e.printStackTrace();
            }
            System.out.println("子线程：" + Thread.currentThread() + "-" + System.currentTimeMillis() + "-end,耗时(ms):" + (System.currentTimeMillis() - stSon));
        });
        System.out.println("主线程：" + Thread.currentThread() + "-" + System.currentTimeMillis() + "-end,耗时(ms):" + (System.currentTimeMillis() - st));

    }
}
```

___

**通过 asyncContext.dispatch 结束异步请求**
可以通过asyncContext.complete()来结束异步请求，结束请求还有另外一种方式，子线程
中处理完毕业务之后，将结果放在request中，然后调用asyncContext.dispatch()转发请求，
此请求又会进入当前servlet，此时需要在代码中判断请求是不是一步转发过来的，然后从request
中获取结果处理.这种方式就是springmvc处理异步的方式.

```java
package com.youaresherlock.demo1;

import jakarta.servlet.AsyncContext;
import jakarta.servlet.DispatcherType;
import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;
import java.util.concurrent.TimeUnit;

//1.设置@WebServlet的asyncSupported属性为true，表示支持异步处理
@WebServlet(name = "AsyncServlet3",
        urlPatterns = "/asyncServlet3",
        asyncSupported = true
)
public class AsyncServlet3 extends HttpServlet {
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("请求类型：" + request.getDispatcherType());
        //@1：判断请求类型，如果是异步类型（DispatcherType.ASYNC），则说明是异步转发过来的，将结果输出
        if (request.getDispatcherType() == DispatcherType.ASYNC) {
            System.out.println("响应结果：" + Thread.currentThread() + "-" + System.currentTimeMillis() + "-start");
            //从request中获取结果，然后输出
            Object result = request.getAttribute("result");
            response.getWriter().write(result.toString());
            System.out.println("响应结果：" + Thread.currentThread() + "-" + System.currentTimeMillis() + "-end");
        } else {
            long st = System.currentTimeMillis();
            System.out.println("主线程：" + Thread.currentThread() + "-" + System.currentTimeMillis() + "-start");
            //2、启动异步处理：调用req.startAsync(request,response)方法，获取异步处理上下文对象AsyncContext
            AsyncContext asyncContext = request.startAsync(request, response);
            //3、调用start方法异步处理，调用这个方法之后主线程就结束了
            asyncContext.start(() -> {
                long stSon = System.currentTimeMillis();
                System.out.println("子线程：" + Thread.currentThread() + "-" + System.currentTimeMillis() + "-start");
                try {
                    //这里休眠2秒，模拟业务耗时
                    TimeUnit.SECONDS.sleep(2);
                    //将结果丢到request中
                    asyncContext.getRequest().setAttribute("result", "ok");
                    //转发请求，调用这个方法之后，请求又会被转发到当前的servlet，又会进入当前servlet的service方法
                    //此时请求的类型（request.getDispatcherType()）是DispatcherType.ASYNC，所以通过这个值可以判断请求是异步转发过来的
                    //然后在request中将结果取出，对应代码@1，然后输出
                    asyncContext.dispatch();
                } catch (Exception e) {
                    e.printStackTrace();
                }
                System.out.println("子线程：" + Thread.currentThread() + "-" + System.currentTimeMillis() + "-end,耗时(ms):" + (System.currentTimeMillis() - stSon));
            });
            System.out.println("主线程：" + Thread.currentThread() + "-" + System.currentTimeMillis() + "-end,耗时(ms):" + (System.currentTimeMillis() - st));
        }
    }
}
```

___

```java
package javax.servlet;

import java.io.*;
import java.util.*;

/**
 * ServletRequest对象用于向servlet提供客户端的请求信息.当servlet容器收到客户端的要求
 * 访问特定的servlet的请求时，容器先解析客户端的原始请求数据，把它包装成一个ServletRequest
 * 对象，将其传递给servlet的service方法
 * 
 * 继承ServletRequest的接口可以提供额外的协议数据支持,如支持http报文数据
 * 的HttpServletRequest接口
 */
public interface ServletRequest {

    /**
     * 返回name对应的value,如果给定name的属性不存在，则返回null
     * 属性设置的方法有两种，一种是servlet容器设置关于请求的自定义信息
     * 比如HTTPS中的SSL证书信息(javax.servlet.request.X509Certificate, Certificate obj)，
     * 另一种是通过ServletRequest#setAttribute设置
     *
     * 属性name应该遵从约定形如java.*、javax.*、sun.*
     */
    public Object getAttribute(String name);
    
    /**
     * 返回此请求的可用的属性name集合
     */
    public Enumeration<String> getAttributeNames();
    
    /**
     * 返回请求体中的编码类型，未指定返回null.
     * 以下指定编码优先级逐渐下降:
     * 1. 请求中指定，在请求头中如Content-Type: text/html; charset=utf-8
     * 2. 每个webapp指定，通过ServletContext#setRequestCharacterEncoding指定
     * 3. 如tomcat通过server.xml配置指定
     */
    public String getCharacterEncoding();

    /**
     * 设置请求体的字符编码类型，此方法必须被优先调用然后再去读取请求体参数或使用getReader方法，
     * 否则没有影响
     * 
     * @throws UnsupportedEncodingException 指定的编码不支持
     */
    public void setCharacterEncoding(String env) throws UnsupportedEncodingException;

    /**
     * 返回请求体的字节长度,未知或长度大于Integer.MAX_VALUE(2^31-1)为-1
     * 也就是请求头中Content-Length: <length>
     */
    public int getContentLength();
    
    /**
     * 同上，为止返回-1L，返回类型为long
     */
    public long getContentLengthLong();
    
    /**
     * 返回请求体的MIME类型，如果类型未知则返回null
     * 即Content-Type: text/html; charset=utf-8中的text/html
     */
    public String getContentType();
    
    /**
     * 返回一个输入流，使用该输入流以二进制方式读取请求体内容
     * 要么使用getInputStream方法要么使用getReader方法，不能同时使用
     * 
     * @exception IllegalStateException 如果对于此请求已经调用过getReader方法则发生此异常
     * @exception IOException 输入输出异常发生
     */
    public ServletInputStream getInputStream() throws IOException; 
     
    /**
     * 返回请求参数的值，请求参数是查询字符串或form表单数据
     *
     * 如果参数只有一个值，那么可以使用此方法，如果可能有多个值，
     * 要使用getParameterValues方法
     * 如果有多个值，使用此方法只会返回getParameterValues方法返回
     * 的第一个值
     */
    public String getParameter(String name);
    
    /**
     * 返回包含在请求中的请求参数名集合
     */
    public Enumeration<String> getParameterNames();
        
    /**
     * 返回name对应的请求参数值的数组
     */
    public String[] getParameterValues(String name);
 
    /**
     * 返回请求参数的map形式，包括所有参数
     */
    public Map<String, String[]> getParameterMap();
    
    /**
     * 返回请求使用的协议名和版本号，形如protocol/majorVersion.minorVersion
     * 如HTTP/1.1与CGI变量SERVER_PROTOCOL相同
     *
     * 也就是从请求行(请求方法空格URL空格协议版本\r\n)中提取的协议版本
     */    
    public String getProtocol();
    
    /**
     * URL的组成部分: scheme://host[:port]/path?querystring#anchor
     * 返回URL中的scheme部分，即请求的协议名
     */
    public String getScheme();
    
    /**
     * 返回hostname
     */
    public String getServerName();
    
    /**
     * 返回请求服务端的port
     */
    public int getServerPort();
    
    /** 
     * 根据请求体的字符编码提取请求体字符流
     *
     * @exception UnsupportedEncodingException  如果不支持客户端请求体的编码，会导致文本不可以被解码
     *
     * @exception IllegalStateException 如果对于此请求，已经调用过getInputStream方法
     *
     * @exception IOException 输入输出异常出现
     */
    public BufferedReader getReader() throws IOException;
    
    /**
     * 返回客户端的ip/最后使用代理服务器的ip
     * 与CGI变量REMOTE_ADDR相同
     */
    public String getRemoteAddr();
    
    /**
     * 返回最后使用代理或者客户端的全限定名,如果引擎不能或为了提高性能不去解析hostname,
     * 此方法返回ip地址
     * 与CGI变量REMOTE_HOST相同
     *
     * 如果主机名是host01，域名是mycompany.com，那么全限定主机名FQDN就是host01.mycompany.com
     */
    public String getRemoteHost();
    
    /**
     * 为此请求设置一个属性，在不同的request对象之间属性会被重置，此
     * 方法通常与RequestDispatcher配合使用
     *
     * 属性name应该遵从约定形如java.*、javax.*、sun.*
     * 如果设置的obj为null,那么相当于remoteAttribute方法.当请求被分发到不同web应用的
     * servlet时，用此方法设置的对象可能不能被调用方提取到
     */
    public void setAttribute(String name, Object o);
    
    /**
     * 从请求对象移除属性
     */
    public void removeAttribute(String name);
    
    /**
     * 根据请求头Accept-Lanaguage来返回客户端本地偏好，如果客户端请求头中不提供此
     * 请求头信息，此方法返回服务端默认的区域
     * 服务端从诸多备用选项中选择一项进行应用，并使用Content-Language应答头通知客户端
     * 它的选择.
     * 
     * Accept-Language请求头的格式: Accept-Language: <language>
     * 如: Accept-Language: fr-CH, fr;q=0.9, en;q=0.8, de;q=0.7, *;q=0.5
     */
    public Locale getLocale();
    
    /**
     * 根据请求头Accept-Language返回客户端接受的地区偏好，根据权重降序排列
     * 如果没有此请求头，则返回一个包含一个服务器默认Local的Enumeration对象
     */
    public Enumeration<Locale> getLocales();
    
    /**
     * 返回请求是否是通过安全通道加密传输，如https
     */
    public boolean isSecure();
    
    /**
     * 与ServletContext#getRequestDispatcher方法类似，区别是此方法可以传入相对路径的参数，
     * 如果路径以"/"开始，那么相对于当前的上下文根路径
     */
    public RequestDispatcher getRequestDispatcher(String path);
    
    /**
     * @param path the path for which the real path is to be returned.
     * 返回path在服务器上的真实路径
     * @deprecated servlet api2.1使用ServletContext#getRealPath方法
     */
    public String getRealPath(String path);
    
    /**
     * 返回客户端/最后使用代理服务器发送请求的源端口
     */    
    public int getRemotePort();

    /**
     * 返回接受请求的主机的hostname
     */
    public String getLocalName();

    /**
     * 返回请求被接受的ip地址
     */       
    public String getLocalAddr();

    /**
     * 返回请求被接受的端口号
     */
    public int getLocalPort();

    /**
     * 获取ServletRequest最后被分发的serlvet
     */
    public ServletContext getServletContext();

    /**
     * 将请求设置为异步模式，使用原始的(unwrapped)ServletRequest和ServletResponse对象来
     * 初始化AsyncContext
     * 
     * @throws IllegalStateException  如果此请求所在的过滤器或servlet不支持异步操作，
     * 即#isAsyncSupported方法返回false, 或者此方法被再次调用不带有异步分发，在分发之外或者
     * 在同一个分发处调用，或者请求已经被关闭.
     */
    public AsyncContext startAsync() throws IllegalStateException;
 
    /**
     * 与上面类似, ServletRequest和ServletResponse需要是相同的实例或者
     * ServletRequestWrapper和ServletResponseWrapper包裹的实例(用于传入
     * Servlet#service、Filter#doFilter方法)，对应的，分别在这些作用域中调用
     * 此方法
     *
     * @throws IllegalStateException 同上
     */
    public AsyncContext startAsync(ServletRequest servletRequest,
                                   ServletResponse servletResponse)
            throws IllegalStateException;
   
    /**
     * 检查此请求是否被设置为异步模式
     * 可以通过#startAsync或#startAsync(ServletRequest,ServletResponse)方法设置
     * 一个ServletRequest对象为异步模式
     *
     * 如果请求被设置为异步模式，但使用AsyncContext#dispatch方法分发出去，或
     * 使用AsyncContext#complete方法释放，则此方法返回false
     */
    public boolean isAsyncStarted();

    /**
     * 返回此请求是否支持异步操作
     * 
     * 如果此请求所在域的过滤器或servlet没有被标注支持异步操作，那么此请求的异步操作被禁用
     * 可以通过在web.xml中使用<async-supported>配置
     * 或使用@WebServlet、@WebFilter中的asyncSupported属性来指定，默认情况下都是false
     */
    public boolean isAsyncSupported();

    /**
     *  获取创建的或者最近使用#startAsync或#startAsync(ServletRequest, ServletResponse)
     * 方法初始化过的AsyncContext实例
     *
     * @throws IllegalStateException 如果此请求没有使用#startAsync或startAsync(ServletRequest, ServletResponse)
     * 方法设置为异步模式
     */
    public AsyncContext getAsyncContext();

    /**
     * 返回此请求的转发类型
     *
     * web容器根据请求的转发类型来选择需要被作用到此请求的过滤器，只有模式类型
     * 和url模式匹配的过滤器才能生效
     * 
     * 请求的初始转发模式为DispacherType.REQUEST.
     * 通过RequestDispatcher#forward(ServletRequest,ServletResponse)
     * 方法转发的请求的转发模式为DispatcherType.FORWARD，通过
     * RequestDispatcher#include(ServletRequest,ServletResponse)
     * 方法转发的请求的转发模式为DispatcherType.INCLUDE.
     * 
     * 对应的，当使用AsyncContext#dispatch方法来转发异步请求的时候，转发模式
     * 为DispatcherType.ASYNC. 最终，通过容器的错误处理机制，会将请求转向一个错误的页面，
     * 转发模式为DispatcherType.ERROR
     */
    public DispatcherType getDispatcherType();
}
```

