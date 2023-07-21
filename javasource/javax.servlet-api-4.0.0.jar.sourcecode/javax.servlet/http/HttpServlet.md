##### HttpServlet
HttpServlet是一个没有抽象方法的抽象类(不想此类被实例化).
1. 继承了GenericServlet类
2. 实现了GenericServlet类的抽象方法service

___

##### javax.servlet.http.HttpServlet
```java
package javax.servlet.http;

import java.io.IOException;
import java.io.PrintWriter;
import java.io.OutputStreamWriter;
import java.io.UnsupportedEncodingException;
import java.lang.reflect.Method;
import java.text.MessageFormat;
import java.util.Enumeration;
import java.util.ResourceBundle;

import javax.servlet.*;

/**
 * 为子类提供了一个抽象类，用于创建适合网站的http servlet.
 * HttpServlet子类必须至少覆写以下方法中的一个:
 * <ul>
 * <li> <code>doGet</code>, 如果servlet支持http get请求
 * <li> <code>doPost</code>, 用于http post请求
 * <li> <code>doPut</code>, 用于http put请求
 * <li> <code>doDelete</code>, 用于http delete请求
 * <li> <code>init</code> and <code>destroy</code>, 
 * 用于管理servlet生命周期持有的资源.
 * <li> <code>getServletInfo</code>, servlet使用此方法来提供关于自己的信息
 * </ul>
 *
 * 几乎没有理由覆写service方法.service根据http请求类型(doXXX方法)来分发
 * 请求到handler方法来处理标准的http请求.
 *
 * 同样的，几乎没有理由覆写doOptions和doTrace方法.
 * 
 * servlets通常运行在多线程的服务上,因此a servlet需要处理并发请求并且
 * 小心同步对共享资源的访问.共享资源包含内存数据，如实例或者类变量以及外部对象，
 * 如文件、数据库连接、以及网络连接.
 */
public abstract class HttpServlet extends GenericServlet
{
    /**
     * HTTP METHOD: 
     * CONNECT、DELETE、GET、HEAD、OPTIONS、PATCH、POST、PUT、TRACE
     */
    private static final String METHOD_DELETE = "DELETE";
    private static final String METHOD_HEAD = "HEAD";
    private static final String METHOD_GET = "GET";
    private static final String METHOD_OPTIONS = "OPTIONS";
    private static final String METHOD_POST = "POST";
    private static final String METHOD_PUT = "PUT";
    private static final String METHOD_TRACE = "TRACE";

    /**
     * If-Modified-Since request HTTP header
     * value: <day-name>, <day> <month> <year> <hour>:<minute>:<second> GMT
     */
    private static final String HEADER_IFMODSINCE = "If-Modified-Since";

    /**
     * Last-Modified request HTTP header
     * value: <day-name>, <day> <month> <year> <hour>:<minute>:<second> GMT
     */
    private static final String HEADER_LASTMOD = "Last-Modified";
    
    private static final String LSTRING_FILE =
        "javax.servlet.http.LocalStrings";

    private static ResourceBundle lStrings =
        ResourceBundle.getBundle(LSTRING_FILE);
    
    /**
     * empty constructor.
     * 创建子类对象的时候，会执行这个无参数构造方法.
     */
    public HttpServlet() { }

    /**
     * 通过service(HttpServletRequest req, HttpServletResponse resp)方法
     * 来处理一个get请求.
     *
     * 覆写此方法可以支持GET请求，同样自动支持了HEAD请求.
     *
     * 覆写此方法后，可以通过读取请求数据、写入响应头以及响应数据.
     * 在获取PrintWriter对象用于返回响应前，通过PrinterWriter对象
     * 设置内容类型和编码类型.
     *
     * servlet容器需要再提交response之前写入响应头.
     *
     * 可以使用javax.servlet.ServletResponse#setContentLength方法
     * 设置Content-Length响应头，容器可以使用持久连接来向客户端返回响应，
     * 以便提升性能.
     * 
     * 如果使用Transfer-Encoding header: chunked分块传输，则不设置Content-Length头.
     *
     * GET方法应该是安全的，没有任何副作用.如，大多数查询操作是无副作用的.
     * 如果一个客户端请求的目的是改变储存的数据，则应该使用其它的HTTP方法.
     *
     * GET方法是幂等的，意味着可以安全的重复调用.通常安全的方法
     * 也是幂等的方法.
     *
     * 如果请求的格式错误，doGet方法返回"400 Bad Request"消息.
     * 
     * @throws IOException I/O Error
     *
     * @throws ServletException 无法处理此GET请求
     */
    protected void doGet(HttpServletRequest req, HttpServletResponse resp)
        throws ServletException, IOException
    {
        String protocol = req.getProtocol();
        // HTTP method GET is not supported by this URL
        String msg = lStrings.getString("http.method_get_not_supported");
        /*
        默认不支持此方法.
        如果是HTTP 1.1，则发送405 Method Not Allowed，否则发送400 Bad Request
        */
        if (protocol.endsWith("1.1")) {
            resp.sendError(HttpServletResponse.SC_METHOD_NOT_ALLOWED, msg);
        } else {
            resp.sendError(HttpServletResponse.SC_BAD_REQUEST, msg);
        }
    }

    /**
     * 返回参数HttpServletRequest req对象上次被修改的时间
     * (自January 1 1970 00:00:00 GMT开始的毫秒数).
     * 如果时间未知，则此方法返回一个负值(此方法默认返回-1).
     *
     * 支持get请求以及可以快速得出上次修改时间的servlet应该覆写此方法.
     * 这会让浏览器和代理缓存更有效地工作，减少服务器和网络资源的负载.
     *
     * @param req 发送到此servlet的HttpServletRequest对象.
     *
     * @return 以long integer指定req对象上次修改的时间
     */
    protected long getLastModified(HttpServletRequest req) {
        return -1;
    }

    /**
     * 从protected service方法接收HEAD请求并处理.
     *
     * 客户端仅仅想要获得的响应头时可以发送HEAD请求，如
     * Content-Type和Content-Length.
     * HEAD方法会精确计算响应体字节数，设置给Content-Length.
     *
     * 如果要覆写此方法，可以避免计算响应体大小，仅仅设置其它响应头
     * 来提高性能.需要确保doHead方法是安全和幂等的.
     *
     * @throws IOException I/O Error
     *
     * @throws ServletException 无法处理HEAD请求
     */
    protected void doHead(HttpServletRequest req, HttpServletResponse resp)
        throws ServletException, IOException
    {
        NoBodyResponse response = new NoBodyResponse(resp);
        
        doGet(req, response);
        response.setContentLength();
    }

    /**
     * POST方法不需要是安全的和幂等的.
     * 
     * @throws IOException I/O Error
     *
     * @throws ServletException 无法处理此请求
     */
    protected void doPost(HttpServletRequest req, HttpServletResponse resp)
        throws ServletException, IOException
    {
        String protocol = req.getProtocol();
        // HTTP method POST is not supported by this URL
        String msg = lStrings.getString("http.method_post_not_supported");
        if (protocol.endsWith("1.1")) {
            resp.sendError(HttpServletResponse.SC_METHOD_NOT_ALLOWED, msg);
        } else {
            resp.sendError(HttpServletResponse.SC_BAD_REQUEST, msg);
        }
    }

    /**
     * 如果覆写此方法，应该保留任何content相关的请求头(包括
     * Content-Length, Content-Type, Content-Transfer-Encoding, 
     * Content-Encoding, Content-Base, Content-Language, Content-Location,
     * Content-MD5, Content-Range).如果PUT方法无法处理content相关请求头，
     * 需要发出一个错误消息(501 Not Implemented).
     * 更多关于HTTP1.1信息，参考RFC 2616 <a href="http://www.ietf.org/rfc/rfc2616.txt"></a>.
     *
     * PUT方法是幂等的、不安全的.
     *
     * @throws IOException I/O Error
     *
     * @throws ServletException 无法处理此请求
     */
    protected void doPut(HttpServletRequest req, HttpServletResponse resp)
        throws ServletException, IOException
    {
        String protocol = req.getProtocol();
        // HTTP method PUT is not supported by this URL
        String msg = lStrings.getString("http.method_put_not_supported");
        if (protocol.endsWith("1.1")) {
            resp.sendError(HttpServletResponse.SC_METHOD_NOT_ALLOWED, msg);
        } else {
            resp.sendError(HttpServletResponse.SC_BAD_REQUEST, msg);
        }
    }

    /**
     * DELETE方法是不安全的、幂等的.
     *
     * @throws IOException I/O Error
     *
     * @throws ServletException 无法处理此请求
     */
    protected void doDelete(HttpServletRequest req,
                            HttpServletResponse resp)
        throws ServletException, IOException
    {
        String protocol = req.getProtocol();
        // Http method DELETE is not supported by this URL
        String msg = lStrings.getString("http.method_delete_not_supported");
        if (protocol.endsWith("1.1")) {
            resp.sendError(HttpServletResponse.SC_METHOD_NOT_ALLOWED, msg);
        } else {
            resp.sendError(HttpServletResponse.SC_BAD_REQUEST, msg);
        }
    }
    
    /**
     * 将HttpServlet子类中的所有声明的方法返回.
     */
    private Method[] getAllDeclaredMethods(Class<? extends HttpServlet> c) {

        Class<?> clazz = c;
        Method[] allMethods = null;

        while (!clazz.equals(HttpServlet.class)) {
            Method[] thisMethods = clazz.getDeclaredMethods();
            if (allMethods != null && allMethods.length > 0) {
                Method[] subClassMethods = allMethods;
                allMethods =
                    new Method[thisMethods.length + subClassMethods.length];
                // 将HttpServlet子类迭代拷贝，返回allMethods.
                System.arraycopy(thisMethods, 0, allMethods, 0,
                                 thisMethods.length);
                System.arraycopy(subClassMethods, 0, allMethods, thisMethods.length,
                                 subClassMethods.length);
            } else {
                allMethods = thisMethods;
            }

            clazz = clazz.getSuperclass();
        }

        return ((allMethods != null) ? allMethods : new Method[0]);
    }

    /**
     * OPTIONS请求检测服务器支持的HTTP请求方法，并返回合适的响应头.
     * 
     * 如果一个servlet覆写了doGet，那么doOptions方法会返回以下响应头: 
     * Allow: GET, HEAD, TRACE, OPTIONS
     *
     * @throws IOException I/O Error
     *
     * @throws ServletException 无法处理此请求
     */
    protected void doOptions(HttpServletRequest req, HttpServletResponse resp)
        throws ServletException, IOException
    {
        // 获取继承HttpServlet子类声明的所有方法(不包括继承的方法)
        Method[] methods = getAllDeclaredMethods(this.getClass());
        
        boolean ALLOW_GET = false;
        boolean ALLOW_HEAD = false;
        boolean ALLOW_POST = false;
        boolean ALLOW_PUT = false;
        boolean ALLOW_DELETE = false;
        // 默认实现trace和options方法.
        boolean ALLOW_TRACE = true;
        boolean ALLOW_OPTIONS = true;
        
        for (int i=0; i<methods.length; i++) {
            String methodName = methods[i].getName();
            
            if (methodName.equals("doGet")) {
                ALLOW_GET = true;
                ALLOW_HEAD = true;
            } else if (methodName.equals("doPost")) {
                ALLOW_POST = true;
            } else if (methodName.equals("doPut")) {
                ALLOW_PUT = true;
            } else if (methodName.equals("doDelete")) {
                ALLOW_DELETE = true;
            }
            
        }
        
        // 调用此方法时自动支持OPTIONS方法.
        StringBuilder allow = new StringBuilder();
        if (ALLOW_GET) {
            allow.append(METHOD_GET);
        }
        if (ALLOW_HEAD) {
            if (allow.length() > 0) {
                allow.append(", ");
            }
            allow.append(METHOD_HEAD);
        }
        if (ALLOW_POST) {
            if (allow.length() > 0) {
                allow.append(", ");
            }
            allow.append(METHOD_POST);
        }
        if (ALLOW_PUT) {
            if (allow.length() > 0) {
                allow.append(", ");
            }
            allow.append(METHOD_PUT);
        }
        if (ALLOW_DELETE) {
            if (allow.length() > 0) {
                allow.append(", ");
            }
            allow.append(METHOD_DELETE);
        }
        if (ALLOW_TRACE) {
            if (allow.length() > 0) {
                allow.append(", ");
            }
            allow.append(METHOD_TRACE);
        }
        if (ALLOW_OPTIONS) {
            if (allow.length() > 0) {
                allow.append(", ");
            }
            allow.append(METHOD_OPTIONS);
        }
        
        resp.setHeader("Allow", allow.toString());
    }
    
    /**
     * 用于debugging，无需覆写.
     * trace方法是不安全的，幂等的.
     *
     * @throws IOException I/O Error
     *
     * @throws ServletException 无法处理此请求
     */
    protected void doTrace(HttpServletRequest req, HttpServletResponse resp) 
        throws ServletException, IOException
    {
        int responseLength;

        // 组成http response header过程
        String CRLF = "\r\n";
        // eg: TRACE /path HTTP 1.1
        StringBuilder buffer = new StringBuilder("TRACE ").append(req.getRequestURI())
            .append(" ").append(req.getProtocol());

        Enumeration<String> reqHeaderEnum = req.getHeaderNames();

        while( reqHeaderEnum.hasMoreElements() ) {
            String headerName = reqHeaderEnum.nextElement();
            buffer.append(CRLF).append(headerName).append(": ")
                .append(req.getHeader(headerName));
        }

        buffer.append(CRLF);

        responseLength = buffer.length();
        
        // message/http media type: used to enclose a single HTTP request or response message
        resp.setContentType("message/http");
        resp.setContentLength(responseLength);
        ServletOutputStream out = resp.getOutputStream();
        out.print(buffer.toString());
    }

    /**
     * 从public service方法接收标准的HTTP请求，并且将它们分发
     * 到此类中定义的doXXX方法.此方法是一个面向HTTP版本的
     * javax.servlet.Servlet#service方法.无需覆写此方法.
     *
     * 遵循RFC 2068 <link>https://www.rfc-editor.org/rfc/rfc2068#page-35</link>
     *
     * 在该方法中定义了核心算法骨架，具体的实现步骤延迟到子类中去完成.
     * @throws IOException I/O Error
     *
     * @throws ServletException 无法处理此http请求
     */
    protected void service(HttpServletRequest req, HttpServletResponse resp)
        throws ServletException, IOException
    {
        String method = req.getMethod();

        if (method.equals(METHOD_GET)) {
            long lastModified = getLastModified(req);
            if (lastModified == -1) {
                // servlet doesn't support if-modified-since, no reason
                // to go through further expensive logic
                doGet(req, resp);
            } else {
                long ifModifiedSince = req.getDateHeader(HEADER_IFMODSINCE);
                // 判断在If-Modified-Since时间之后有无修改请求的资源
                if (ifModifiedSince < lastModified) {
                    // ifModifiedSince为-1总是成立.                    
                    // 如果If-Modified-Since时间之后有修改资源，则调用doGet()
                    // 同时更新资源的修改时间.
                    maybeSetLastModified(resp, lastModified);
                    doGet(req, resp);
                } else {
                    // 304 Not Modified
                    resp.setStatus(HttpServletResponse.SC_NOT_MODIFIED);
                }
            }
        } else if (method.equals(METHOD_HEAD)) {
            long lastModified = getLastModified(req);
            maybeSetLastModified(resp, lastModified);
            doHead(req, resp);
        } else if (method.equals(METHOD_POST)) {
            doPost(req, resp);
        } else if (method.equals(METHOD_PUT)) {
            doPut(req, resp);
        } else if (method.equals(METHOD_DELETE)) {
            doDelete(req, resp);
        } else if (method.equals(METHOD_OPTIONS)) {
            doOptions(req,resp);
        } else if (method.equals(METHOD_TRACE)) {
            doTrace(req,resp);
            // servlet不支持RFC2069定义的方法
        } else {
            // errMsg: Method {method} is not defined in RFC 2068 and is not supported by the Servlet API 
            String errMsg = lStrings.getString("http.method_not_implemented");
            Object[] errArgs = new Object[1];
            errArgs[0] = method;
            errMsg = MessageFormat.format(errMsg, errArgs);
            
            // status code: 501 not implemented
            resp.sendError(HttpServletResponse.SC_NOT_IMPLEMENTED, errMsg);
        }
    }
    
    /*
     * 如果响应头没有被设置Last-Modified字段，并且lastModified值合法(>=0)，则
     * 设置.在doGet方法调用之前设置，来确保在response数据提交之前设置好响应头.
     */
    private void maybeSetLastModified(HttpServletResponse resp,
                                      long lastModified) {
        if (resp.containsHeader(HEADER_LASTMOD))
            return;
        if (lastModified >= 0)
            resp.setDateHeader(HEADER_LASTMOD, lastModified);
    }
    
    /**
     * 此方法会将客户端的请求分发到protected的service方法.无需覆写此方法.
     *
     * @throws IOException I/O Error
     *
     * @throws ServletException 如果无法处理客户端请求或req不是HttpServletRequest
     * 的实例(或res不是HttpServletResponse的实例)
     */
    @Override
    public void service(ServletRequest req, ServletResponse res)
        throws ServletException, IOException
    {
        HttpServletRequest  request;
        HttpServletResponse response;
        
        if (!(req instanceof HttpServletRequest &&
                res instanceof HttpServletResponse)) {
            throw new ServletException("non-HTTP request or response");
        }

        request = (HttpServletRequest) req;
        response = (HttpServletResponse) res;

        service(request, response);
    }
}

/*
 * 没有响应体的response.用于对HEAD请求的支持.
 * 在忽略body的同时会计算响应体的字节数组，以便于设置给Content-Length响应头.
 */
// file private
class NoBodyResponse extends HttpServletResponseWrapper {

    private static final ResourceBundle lStrings
        = ResourceBundle.getBundle("javax.servlet.http.LocalStrings");

    private NoBodyOutputStream noBody;
    private PrintWriter writer;
    private boolean didSetContentLength;
    private boolean usingOutputStream;

    // file private
    NoBodyResponse(HttpServletResponse r) {
        super(r);
        noBody = new NoBodyOutputStream();
    }

    // file private
    void setContentLength() {
        if (!didSetContentLength) {
            if (writer != null) {
                // 主动去刷新缓冲区(创建writer时指定不自动刷新)
                writer.flush();
            }
            setContentLength(noBody.getContentLength());
        }
    }

    @Override
    public void setContentLength(int len) {
        super.setContentLength(len);
        didSetContentLength = true;
    }

    @Override
    public void setContentLengthLong(long len) {
        super.setContentLengthLong(len);
        didSetContentLength = true;
    }

    @Override
    public void setHeader(String name, String value) {
        super.setHeader(name, value);
        checkHeader(name);
    }

    @Override
    public void addHeader(String name, String value) {
        super.addHeader(name, value);
        checkHeader(name);
    }

    @Override
    public void setIntHeader(String name, int value) {
        super.setIntHeader(name, value);
        checkHeader(name);
    }

    @Override
    public void addIntHeader(String name, int value) {
        super.addIntHeader(name, value);
        checkHeader(name);
    }

    private void checkHeader(String name) {
        // 如设置Content-Length，标记didSetContentLength
        if ("content-length".equalsIgnoreCase(name)) {
            didSetContentLength = true;
        }
    }

    @Override
    public ServletOutputStream getOutputStream() throws IOException {
        // 如果之前已经调用过了getWriter方法则抛出IllegalStateException异常
        if (writer != null) {
            // Illegal to call getOutputStream() after getWriter() has been called
            throw new IllegalStateException(
                lStrings.getString("err.ise.getOutputStream"));
        }
        usingOutputStream = true;

        return noBody;
    }

    @Override
    public PrintWriter getWriter() throws UnsupportedEncodingException {
        // 如果之前已经调用过getWriter方法则抛出IllegalStateException异常
        if (usingOutputStream) {
            // Illegal to call getWriter() after getOutputStream() has been called
            throw new IllegalStateException(
                lStrings.getString("err.ise.getWriter"));
        }

        if (writer == null) {
            OutputStreamWriter w = new OutputStreamWriter(
                noBody, getCharacterEncoding());
            writer = new PrintWriter(w);
        }

        return writer;
    }
}

/*
 * Servlet输出流，会忽略所有写入的数据.
 */
// file private
class NoBodyOutputStream extends ServletOutputStream {

    private static final String LSTRING_FILE =
        "javax.servlet.http.LocalStrings";
    private static ResourceBundle lStrings =
        ResourceBundle.getBundle(LSTRING_FILE);

    private int contentLength = 0;

    // file private
    NoBodyOutputStream() {}

    // file private
    int getContentLength() {
        return contentLength;
    }

    @Override
    public void write(int b) {
        contentLength++;
    }

    @Override
    public void write(byte buf[], int offset, int len)
        throws IOException
    {
        if (buf == null) {
            // Null passed for byte array in write method
            throw new NullPointerException(
                    lStrings.getString("err.io.nullArray"));
        }
        // 如果offset、len不合法，抛出数组越界异常.
        if (offset < 0 || len < 0 || offset+len > buf.length) {
            String msg = lStrings.getString("err.io.indexOutOfBounds");
            Object[] msgArgs = new Object[3];
            msgArgs[0] = Integer.valueOf(offset);
            msgArgs[1] = Integer.valueOf(len);
            msgArgs[2] = Integer.valueOf(buf.length);
            msg = MessageFormat.format(msg, msgArgs);
            /*
             msg:
             Invalid offset [offset] and / or length [len] specified for array of size [buf.length]
             */
            throw new IndexOutOfBoundsException(msg);
        }

        contentLength += len;
    }

    public boolean isReady() {
        // 总是不能写入数据的.
        return false;
    }

    public void setWriteListener(WriteListener writeListener) {

    }
}
```