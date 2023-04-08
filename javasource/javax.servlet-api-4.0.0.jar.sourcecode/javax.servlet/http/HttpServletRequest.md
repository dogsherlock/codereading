##### javax.servlet.http.HttpServletRequest
```java
package javax.servlet.http;

import java.io.IOException;
import java.util.*;
import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;

/**
 * 继承javax.servlet.ServletRequest接口来为HTTP servlets提供请求信息.
 * 
 * 由容器创建HttpServletRequest对象并传递给Servlet的service方法
 *
 * HTTP1.1使用的认证方式有: BASIC认证、DIGEST认证、SSL客户端认证、FORMBASE认证
 */
public interface HttpServletRequest extends ServletRequest {

    /**
     * 基本认证
     * WWW-Authenticate: Basic realm=<realm>, charset="UTF-8"
     * Authorization: Basic <credentials>
     */
    public static final String BASIC_AUTH = "BASIC";

    /**
     * FROM表单认证
     */
    public static final String FORM_AUTH = "FORM";

    /**
     * 客户端证书认证(SSL认证)
     */
    public static final String CLIENT_CERT_AUTH = "CLIENT_CERT";

    /**
     * DIGEST认证
     */
    public static final String DIGEST_AUTH = "DIGEST";

    /**
     * 返回认证类型.所有的servlet容器支持基本认证、表单认证、客户端证书认证，
     * 也许还有附加的摘要认证.
     * 如无需认证，则返回null.
     *
     * 与CGI的AUTH_TYPE类似
     */
    public String getAuthType();

    /**
     * 返回客户端发送的请求携带的所有cookie信息.
     * 如果请求不含有cookie，则返回null.
     */
    public Cookie[] getCookies();

    /**
     * 返回指定请求头的值表示的日期对象的long值.
     * 在包含日期的请求头中使用此方法，如If-Modified-Since
     * If-Modified-Since表示服务区器只在所请求的资源在给定
     * 的日期时间之后对内容进行过修改的情况下才会将资源返回，
     * 否则返回304.
     *
     * 如 If-Modified-Since: Wed, 21 Oct 2015 07:28:00 GMT
     *
     * 返回的日期会以January 1, 1970 GMT后的毫秒数返回.请求头
     * key对大小写不敏感.
     *
     * 如果请求不包含指定的请求头，则此方法返回-1.如果转换
     * 失败，则抛出IllegalArgumentException.
     *
     * @exception	IllegalArgumentException 如果请求头的值不能被转换成日期
     */
    public long getDateHeader(String name);

    /**
     * 根据指定的请求头name(不区分大小写)返回value值.如果不存在，则返回null.
     * 如果有多个相同name的请求头，此方法返回请求中的第一个.
     */
    public String getHeader(String name);

    /**
     * 根据指定请求头name获取value的集合，如果没有，则返回空集合.
     * 如果容器不允许访问此头信息，则返回null.
     *
     * 如客户端可以发送多个头Accept-Language，每个带有不同的值，而不是用逗号分割的列表.
     */
    public Enumeration<String> getHeaders(String name);

    /**
     * 获取请求中包含的所有请求头name集合.如果没有，返回空集合.
     *
     * 一些servlet容器不允许servlet去使用此方法访问请求头，
     * 这种情况下返回null.
     */
    public Enumeration<String> getHeaderNames();

    /**
     * 返回指定请求头的值(int)，如果没有，返回-1.如果此请求头不能被转换
     * 成整型，此方法抛出NumberFormatException异常.
     *
     * @exception	NumberFormatException 请求头值不能转换为整型	
     */
    public int getIntHeader(String name);

    /**
     * 返回可以在运行时动态发现Servlet的URL映射信息
     * 遵循servlet4.0的实现必须重写此方法，返回的对象是不可变的.
     */
    default public HttpServletMapping getHttpServletMapping() {
        return new HttpServletMapping() {
            @Override
            public String getMatchValue() {
                return "";
            }

            @Override
            public String getPattern() {
                return "";
            }

            @Override
            public String getServletName() {
                return "";
            }

            @Override
            public MappingMatch getMappingMatch() {
               return null;
            }

            @Override
            public String toString() {
                return "MappingImpl{" + "matchValue=" + getMatchValue()
                        + ", pattern=" + getPattern() + ", servletName=" 
                        + getServletName() + ", mappingMatch=" + getMappingMatch() 
                        + "} HttpServletRequest {" + HttpServletRequest.this.toString()
                        + '}';
            }
        };
    }
    
    /**
     * 返回请求方法.与CGI变量REQUEST_METHOD相同.
     * 如GET、POST、PUT等.
     */
    public String getMethod();

    /**
     * 返回与客户端发出此请求时发送的URL相关联的额外的路径信息.
     * 额外的路径信息在servlet路径之后，位于查询字符串之前，以"/"开头.
     *
     * 如果没有额外的路径信息，则返回null.
     * 例如：url-pattern配置为/demo/*，请求URL为
     * http://localhost/pro/demo/htm/index.html，则pathInfo为/htm/index.html.
     * 
     * 与CGI变量PATH_INFO相同.
     */
    public String getPathInfo();

    /**
     * 返回servlet路径之后查询字符串之前的额外路径转换后的真实路径.
     * 与CGI变量PATH_TRANSLATED相同.
     *
     * 如果没有额外的路径信息或servlet容器不能将虚拟路径转化为真实路径，
     * 返回null.
     *
     * web容器不会解码此字符串.
     */
    public String getPathTranslated();

    /**
     * 实例化PushBuilder对象用于服务器向当前请求推送响应.如果当前连接
     * 不支持服务端推送，或者客户端通过settings帧SETTINGS_ENABLE_PUSH
     * 设置为0禁用服务端推送，则此方法返回null.
     */
     default public PushBuilder newPushBuilder() {
         return null;
     }

    /**
     * 返回项目根路径.
     * 例如：url-pattern配置为/demo/*，请求URL为
     * http://localhost/pro/demo/htm/index.html，则contextPath为/demo.
     * 上下文路径以"/"但不以"/"结尾.对于默认上下文的servlet，此方法返回"".
     * 容器不会解码此字符串.
     * 
     * 一个servlet容器可能一个上下文匹配多个上下文路径.此时此方法将会返回
     * 被请求使用的实际上下文路径，可能与javax.servlet.ServletContext#getContextPath
     * 返回的路径不同.
     * 
     * javax.servlet.ServletContext#getContextPath返回的上下文路径应该是被视作
     * 此应用首要的或偏好的上下文路径.
     */
    public String getContextPath();

    /**
     * 返回请求URL中的查询字符串.如果没有，返回null.与CGI变量QUERY_STRING相同.
     */
    public String getQueryString();

    /**
     * 如果已经验证过用户，则返回发出此请求的用户的登录信息.如果用户没有
     * 经过验证，则返回null. 用户名是否随每个后续请求发送取决于浏览器和验证类型，
     * 返回的值与CGI变量REMOTE_USER的值相同.
     */
    public String getRemoteUser();

    /**
     * 检查指定的逻辑"角色"中是否包含经过验证的用户.角色和角色成员关系可以使用
     * 部署描述符定义.如果用户没有经过验证，此方法返回false.
     *
     * 不要使用*作为isUserInRole的参数，否则返回false.
     */
    public boolean isUserInRole(String role);

    /**
     * 返回包含当前请求对应的已经验证过的用户的名称的java.security.Principal
     * 对象如果用户没有经过验证，则该方法返回null.
     */
    public java.security.Principal getUserPrincipal();

    /**
     * 获取标示客户端的sessionid.可能与此请求当前的有效session不同.
     */
    public String getRequestedSessionId();

    /**
     * 返回HTTP请求行中的URL，不包括查询字符.
     * For example:
     * <table summary="Examples of Returned Values">
     * <tr align=left><th>First line of HTTP request      </th>
     * <th>     Returned Value</th>
     * <tr><td>POST /some/path.html HTTP/1.1<td><td>/some/path.html
     * <tr><td>GET http://foo.bar/a.html HTTP/1.0
     * <td><td>/a.html
     * <tr><td>HEAD /xyz?a=b HTTP/1.1<td><td>/xyz
     * </table>
     */
    public String getRequestURI();

    /**
     * 请求URL的地址，包含协议、主机名、端口号和服务器路径，但不包括查询字符串.
     *
     * 如果使用过javax.servlet.RequestDispatcher#forward转发此请求，服务器路径
     * 必须反应获得RequestDispatcher对象的路径，而不是客户端指定的服务器路径.
     *
     * 为了便于修改URL，返回StringBuffer类型而不是String类型
     *
     * 在创建重定向信息和错误报告上，此方法很有用.
     */
    public StringBuffer getRequestURL();

    /**
     * 返回请求的URL中调用servlet的部分.此路径以"/"开始，要么
     * 包含servlet名或者通往servlet的路径，并且不包含查询字符串等
     * 额外的信息.与CGI变量SCRIPT_NAME相同.
     * 其实真是的意思就是，在配置webx.xml或编程式配置时，配置了url-pattern，
     * 请求的URL与url-pattern的有效部分重合部分就是servletPath，
     * 
     * 例如：url-pattern配置为/demo/*，请求URL为
     * http://localhost/pro/demo/htm/index.html，则servletPath为/demo.
     * 如果url-pattern为/*，则此方法返回空字符串.
     */
    public String getServletPath();

    /**
     * 返回请求对应的当前的session.
     * 在没有session的情况下:
     * create=true，则创建一个新的session.
     * create=false,则不创建session，返回null.
     */
    public HttpSession getSession(boolean create);

    /**
     * 返回于此请求关联的session，如果没有则创建一个.
     */
    public HttpSession getSession();

    /**
     * 改变与当前请求关联的session的id，并返回新的session id.
     *
     * @throws IllegalStateException 此请求没有关联的session
     */
    public String changeSessionId();

    /**
     * 检查sessionid是否有效.如果客户端没有进行指定sessionID，则此方法返回false.
     */
    public boolean isRequestedSessionIdValid();

    /**
     * 检查sessionid是否从cookie中获得.
     */
    public boolean isRequestedSessionIdFromCookie();

    /**
     * 检查sessionid是否从URL中获得.
     */
    public boolean isRequestedSessionIdFromURL();

    /**
     * @deprecated As of Version 2.1 of the Java Servlet
     *				API, use {@link #isRequestedSessionIdFromURL}
     *				instead.
     */
    @Deprecated
    public boolean isRequestedSessionIdFromUrl();

    /**
     * 使用为ServletContext配置的容器登录机制来认证发起此请求
     * 的用户. 此方法可能修改和提交参数HttpServletResponse对象.
     *
     * @throws IOException 从此request读取或给定的response写入发生I/O错误.
     *
     * @throws IllegalStateException 如果登录机制试图在response提交之后修改此
     * response.
     *
     * @throws ServletException 验证失败，调用者负责处理错误(即，底层登录机制没有
     * 建立内容和http状态码来返回给用户)
     */
    public boolean authenticate(HttpServletResponse response)
	throws IOException,ServletException;

    /**
     * 为ServletContext配置的web容器登陆机制验证参数username和password
     *
     * @exception	ServletException 如果配置的登录机制不支持用户名和密码验证
     * ，或已经建立，或验证失败.
     */
    public void login(String username, String password)
	throws ServletException;

    /**
     * 退出登录.
     * 将在此请求上调用的getUserPrincipal、getRemoteUser、getAuthType
     * 返回的值设置为null.
     *
     * @exception ServletException 退出登录失败.
     */
    public void logout() throws ServletException;

    /**
     * 获取请求中所有的part部分，类型是multipart/form-data.
     * 也就是Content-Disposition通用标头对应的multipart消息体
     * 的子部分中.
     *
     * 如果请求是multipart/form-data类型，但不包含任何part部分，则返回空的
     * Collection.
     *
     * 任何对返回的Collection的修改不会影响HttpServletRequest obj.
     *
     * @throws IOException 在提取请求的part部分时出现I/O错误.
     *
     * @throws ServletException 如果请求类型不是multipart/form-data
     *
     * @throws IllegalStateException 如果请求体大于maxRequestSize，或者
     * 请求中的任何part部分大于maxFileSize，或者部署描述符中没有
     * @MultipartConfig或multipart-config
     */
    public Collection<Part> getParts() throws IOException, ServletException;

    /**
     * 获取指定名字的part
     * 如Content-Disposition: form-data; name="fieldName"; filename="filename.jpg"
     * 中的name参数来标识相关part.
     *
     * @throws IOException 提取请求对象的part部分发生I/O错误

     * @throws ServletException 请求类型不是multipart/form-data
     * 
     * @throws IllegalStateException 如果请求体大于maxRequestSize，或者
     * 请求中的任何part部分大于maxFileSize，或者部署描述符中没有
     * @MultipartConfig或multipart-config
     */
    public Part getPart(String name) throws IOException, ServletException;

    /**
     * 根据参数handlerClass创建HttpUpgradeHandler实例，并用于http协议升级处理.
     *
     * @exception IOException 协议升级发生I/O错误
     *
     * @exception ServletException 给定参数handlerClass实例化失败
     */
    public <T extends HttpUpgradeHandler> T  upgrade(Class<T> handlerClass)
        throws IOException, ServletException;

    /**
     * Trailer是一个响应首部，允许发送方在分块发送的消息后面添加额外的元信息.
     * 如: 
     * HTTP/1.1 200 OK
     * Content-Type: text/plain
     * Transfer-Encoding: chunked
     * Trailer: Expires

     * 7\r\n
     * Mozilla\r\n
     * 9\r\n
     * Developer\r\n
     * 7\r\n
     * Network\r\n
     * 0\r\n
     * Expires: Wed, 21 Oct 2015 07:28:00 GMT\r\n
     * \r\n
     * 
     * 获取request trailer字段，修改HttpServletRequest对象和返回的map不会互相影响.
     * 
     * 要先调用#isTrailerFieldsReady方法来确保可以安全调用此方法，不会造成异常.
     * 
     * @return 返回trailer字段的map，所有的key是小写的.如果没有trailer字段，
     * #isTrailerFiledsReady返回true，返回空map.
     *
     * @throws IllegalStateException #isTrailerFieldsReady返回false
     */
    default public Map<String, String> getTrailerFields() {
        return Collections.emptyMap();
    }

    /**
     * 返回是否可以是使用#getTrailerFields来读取trailer字段.
     *
     * 如果请求中没有trailer，则立即返回true，如底层协议不支持trailer字段(只有HTTP1.1支持)，
     * 或者请求不是分块传输(Transfer-Encoding: chunked).
     * 如果以下两个条件都满足，则返回true.
     * <ol type="a">
     *   <li> 应用已经读取了所有的请求数据(#getReader/#getInputStream)，并且读取到了文件结尾(EOF).
     *   <li> 所有被客户端发送的trailer字段都被接受到了(可能没有发送)
     * </ol>
     */
    default public boolean isTrailerFieldsReady() {
        return true;
    }
}
```