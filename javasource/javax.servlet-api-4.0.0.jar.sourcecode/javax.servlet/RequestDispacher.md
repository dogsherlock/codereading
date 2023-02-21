##### javax.servlet.RequestDispatcher
```java
package javax.servlet;

import java.io.IOException;

/**
 * RequestDispatcher从客户端接收请求,并将其发送到服务器上的资源(例如Servlet, HTML文件, JSP文件)
 */
 
public interface RequestDispatcher {

    /**
     * forward之后request.getAttribute()可以通过此name获得上一次的request uri
     */
    static final String FORWARD_REQUEST_URI = "javax.servlet.forward.request_uri";

    /**
     * 类似同上
     */
    static final String FORWARD_CONTEXT_PATH = "javax.servlet.forward.context_path";

    /**
     * forward后可以通过request.getHttpServletMapping()获取上一个url映射规则
     */
    static final String FORWARD_MAPPING = "javax.servlet.forward.mapping";

    /**
     * 同上pathinfo
     */
    static final String FORWARD_PATH_INFO = "javax.servlet.forward.path_info";

    /**
     * 同上servletpath
     */
    static final String FORWARD_SERVLET_PATH = "javax.servlet.forward.servlet_path";

    /**
     * 同上servletinfo
     */
    static final String FORWARD_QUERY_STRING = "javax.servlet.forward.query_string";

    /**
     * 同上requesturi
     */
    static final String INCLUDE_REQUEST_URI = "javax.servlet.include.request_uri";

    /**
     * 同上contextpath
     */
    static final String INCLUDE_CONTEXT_PATH = "javax.servlet.include.context_path";

    /**
     * 同上pathinfo
     */
    static final String INCLUDE_PATH_INFO = "javax.servlet.include.path_info";

    /**
     * 同上HttpServletMapping
     */
    static final String INCLUDE_MAPPING = "javax.servlet.include.mapping";

    /**
     * 同上servletpath
     */
    static final String INCLUDE_SERVLET_PATH = "javax.servlet.include.servlet_path";

    /**
     * 同上querystring
     */
    static final String INCLUDE_QUERY_STRING = "javax.servlet.include.query_string";

    /**
     * 发生错误的dispatch的exception obj信息传递给request
     */
    public static final String ERROR_EXCEPTION = "javax.servlet.error.exception";

    /**
     * 发生错误的exception type
     */
    public static final String ERROR_EXCEPTION_TYPE = "javax.servlet.error.exception_type";

    /**
     * 发生错误的exception message
     */
    public static final String ERROR_MESSAGE = "javax.servlet.error.message";

    /**
     * 同上request uri
     */
    public static final String ERROR_REQUEST_URI = "javax.servlet.error.request_uri";

    /**
     * 同上servlet name
     */
    public static final String ERROR_SERVLET_NAME = "javax.servlet.error.servlet_name";

    /**
     * 同上status code
     */
    public static final String ERROR_STATUS_CODE = "javax.servlet.error.status_code";


    /**
     * forward 方法可以允许一个 servlet 对请求做一些前置的处理, 然后传递给另一个资源
     * 来生成响应(资源如: servelt, jsp文件, html文件)
     * 此方法将请求的dispatcher类型设置为DispatcherType.FORWARD
     */
    public void forward(ServletRequest request, ServletResponse response)
        throws ServletException, IOException;

    /**
     * 此方法用于包含响应中某个资源（servlet、JSP 页面和 HTML 文件）的内容
     * 调用者指定一个被包含的资源，将这个包含的资源 (JSP,Servlet，HTML)
     * 的响应数据包含到自己的响应体中。被包含的数据是在服务器上经过运行产生的，
     * 因此是动态包含
     * 被包含的servlet不能改变响应状态码和设置状态行,任何改变都会被忽略, 此方法将
     * 设置 request 的 dispatcher 类型为DispatcherType.INCLUDE
     */
    public void include(ServletRequest request, ServletResponse response)
        throws ServletException, IOException;
}
```