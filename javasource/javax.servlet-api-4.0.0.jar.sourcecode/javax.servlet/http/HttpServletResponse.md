##### javax.servlet.http.HttpServletResponse
```java
package javax.servlet.http;

import java.io.IOException;
import java.util.Collection;
import java.util.Map;
import java.util.function.Supplier;
import javax.servlet.ServletResponse;

/**
 * 继承ServletResponse接口来提供HTTP协议在发送响应的功能.
 * 如，此接口有访问HTTP首部和cookie的方法.
 *
 * servlet容器创建HttpServletResponse实例，并将其作为参数传给servlet的service
 * 方法(doGet、doPost等等).
 */
public interface HttpServletResponse extends ServletResponse {

    /**
     * 将指定的cookie加入到response中.可以多次调用此方法来设置更多的cookie.
     */
    public void addCookie(Cookie cookie);

    /**
     * 检查response中是否有指定name的首部.
     */
    public boolean containsHeader(String name);

    /**
     * 对指定URL进行编码，编码后包含会话ID，若逻辑判断不需要编码，则返回原URL.
     * 对此方法的实现需包括: 确定会话ID是否需要包含在编码后的URL中，当浏览器允许
     * cookie或URL重写(会话跟踪)被关闭时，则不需要编码URL.
     * 
     * 对于健壮的会话各种，Servlet发出的所有URL都应该通过这个方法运行，否则，
     * URL重写不能用于不支持cookie的浏览器.
     * 
     * 如果参数URL是相对的，则相对于当前的HttpServletRequest.
     *
     * @exception IllegalArgumentException url无效.
     */
    public String encodeURL(String url);

    /**
     * 编码用在sendRedirect的指定URL，如不需要，返回原URL.
     * 此方法包含会话ID的逻辑与encodeURL(String url)是不同的，所以进行了分离.
     * 
     * 所有发送到HttpServletResponse.sendRedirect的URL应该通过此方法运行，否则，
     * URL重写不能用于支持cookies的浏览器.
     *
     * 如果参数URL是相对的，则相对于当前的HttpServletRequest.
     * 
     * @exception IllegalArgumentException url无效.
     */
    public String encodeRedirectURL(String url);

    /**
     * @deprecated 到version 2.1为止, 使用encodeURL(String url)代替.
     *
     * @exception IllegalArgumentException url无效.
     */
    @Deprecated
    public String encodeUrl(String url);
    
    /**
     * @deprecated 到version 2.1为止, encodeRedirectURL(String url)代替.
     * 
     * @exception IllegalArgumentException url无效.
     */
    @Deprecated
    public String encodeRedirectUrl(String url);

    /**
     * 写入指定的状态码和错误描述信息，并清除缓冲区，将错误响应给客户端.
     * 默认返回的MIME类型是"text/html".调用者并不会对当前response的编码和MIME类型
     * 负责，这是容器层面的安全责任.
     *
     * 如果web应用对指定状态码有错误页面描述，可能会忽略参数msg.
     *
     * 使用此方法后，response被视作已经提交，不能被写入.
     * 
     * @exception	IOException	I/O Error
     *
     * @exception	IllegalStateException response已经被提交.
     */
    public void sendError(int sc, String msg) throws IOException;

    /**
     * 写入指定的状态码，并清除缓冲区，将错误响应给客户端.
     *
     * @exception	IOException I/O Error
     * @exception	IllegalStateException response已经被提交.
     */
    public void sendError(int sc) throws IOException;

    /**
     * 使用指定的重定向URL向客户端发送临时重定向响应.
     * 调用此方法会将状态码设置为#SC_FOUND 302 (Found).
     * 此方法可以使相对URL，servlet容器需要转化为绝对URL，然后给客户端发送响应.
     * 不是以"/"开头的相对路径是相对于当前请求URL.
     * 以"/"开头的相对路径是相对于当servlet容器的根路径.
     * 以"//"开头的相对路径是network-path(<a href="http://www.ietf.org/rfc/rfc3986.txt">)
     * 
     * @exception	IOException 
     * @exception	IllegalStateException response已经被提交或者localtion不能转化为有效的URL.
     */
    public void sendRedirect(String location) throws IOException;
    
    /**
     * 设置Date类型的响应头(date为自epoch的毫秒数)，若已经存在指定的头，则会覆盖.
     */
    public void setDateHeader(String name, long date);
    
    /**
     * 新增Date类型头信息.不管是否存在，都会追加，可允许存在多个同名的头.
     */
    public void addDateHeader(String name, long date);
    
    /**
     * 使用给定的name和value设置响应头，如果已经存在，则会覆盖.
     * 
     * @param	name 响应头name
     * @param	value 响应头value  如果包含octet string(16进制表示的字节序列)，
     * 应该根据RFC 2047编码(http://www.ietf.org/rfc/rfc2047.txt)
     */
    public void setHeader(String name, String value);
    
    /**
     * 新增头信息.不管是否存在，都会追加，可允许存在多个同名的头.
     * 
     * @param	name 响应头name
     * @param	value 响应头追加value  如果包含octet string(16进制表示的字节序列)，
     * 应该根据RFC 2047编码(http://www.ietf.org/rfc/rfc2047.txt)
     */
    public void addHeader(String name, String value);

    /**
     * 设置int类型头信息.若已经存在指定的头，则会覆盖.
     */
    public void setIntHeader(String name, int value);

    /**
     * 新增int类型头信息.不管是否存在，都会追加，可允许存在多个同名的头.
     */
    public void addIntHeader(String name, int value);

    /**
     * 设置response的状态码.
     * 
     * 此方法被用来设置成功的状态码，出现错误设置状态码可以使用#sendError.
     * 如果使用此方法发送一个错误状态码，则容器的错误页面机制不会被触发.
     * 例如: SC_OK、SC_MOVED_TEMPORARILY
     *
     * 此方法会保留cookie和响应头.
     * 
     * 有效状态码范围2XX, 3XX, 4XX, and 5XX，其它状态码被视为容器特有的.
     */
    public void setStatus(int sc);

    /**
     * 为response设置状态码和内容.
     * 
     * @deprecated 截止version 2.1, 由于参数ms的意义不明确.
     * 使用#setStatu(int)设置状态码，使用sendError(int, String)发送带有描述信息的错误.
     * Sets the status code and message for this response.
     */
    @Deprecated
    public void setStatus(int sc, String sm);

    /**
     * 获取此response的当前状态码.
     */
    public int getStatus();

    /**
     * 使用参数name获取response响应头的值.
     * 如果存在多个值，返回第一个；如果不存在，返回null.
     *
     * 此方法只考虑通过#setHeader、#addHeader、#setDateHeader、#addDateHeader、
     * #setIntHeader、#addIntHeader设置或添加的响应头.
     */
    public String getHeader(String name); 

    /**
     * 使用参数name获取response响应头的所有值.
     * 
     * 对返回集合的改变不会影响HttpServletResponse obj.
     */			
    public Collection<String> getHeaders(String name); 
    
    /**
     * 返回response的响应头的name集合.
     */
    public Collection<String> getHeaderNames();

    /**
     * 设置trailer字段的supplier.
     *
     * 参考RFC 7230(Trailer Field)，违反了4.1.2章节的trailer将会被忽略.
     *
     * supplier将会在造成response内容写入完成的线程和调用中被调用.通常在
     * 输出流中调用close()的线程处调用.
     *
     * @exception IllegalStateException response已经提交或trailer在请求中没有被支持，
     * 如，底层的协议是HTTP1.0或HTTP1.1响应不是chunked编码(Transfer-Encoding: chunked).
     */
    default public void setTrailerFields(Supplier<Map<String, String>> supplier) {
    }

    /**
     * 获取trailer字段的supplier.
     */
    default public Supplier<Map<String, String>> getTrailerFields() {
        return null;
    }

    /*
     * 状态码参考RFC2068(Status Code and Reason Phrase)
     */

    /**
     * 100 Continue
     * 表示客户端应该继续请求.
     */
    public static final int SC_CONTINUE = 100;

    /**
     * 101 Switching Protocol
     * 表示服务器应客户端升级协议的请求(Upgrade请求头)正在切换协议.
     */
    public static final int SC_SWITCHING_PROTOCOLS = 101;

    /**
     * 200 OK
     * 表示请求成功.
     */
    public static final int SC_OK = 200;

    /**
     * 201 Created
     * 表示请求成功并且创建了一个新的资源.
     */
    public static final int SC_CREATED = 201;

    /**
     * 202 Accepted
     * 表示请求已经被接受，并且被处理，但处理还没有完成.
     */
    public static final int SC_ACCEPTED = 202;

    /**
     * 203 Non-Authoritative Information
     * 表示获得的元数据不是来源于服务器.
     */
    public static final int SC_NON_AUTHORITATIVE_INFORMATION = 203;

    /**
     * 204 No Content
     * 表示请求成功，没有新的信息返回.
     */
    public static final int SC_NO_CONTENT = 204;

    /**
     * 205 Reset Content
     * 用来通知客户端重置文档视图.
     */
    public static final int SC_RESET_CONTENT = 205;

    /**
     * 206 Partial Content
     * 表示请求成功，响应体包含所请求的数据区间，
     * 该数据区间是在请求的Range首部指定的.
     */
    public static final int SC_PARTIAL_CONTENT = 206;

    /**
     * 300 Multiple Choices
     * 表示该请求拥有多种可能的响应.
     */
    public static final int SC_MULTIPLE_CHOICES = 300;

    /**
     * 301 Moved Permanently
     * 说明请求的资源已经被移动到了由Location头部指定的url上，
     * 不会再改变.
     */
    public static final int SC_MOVED_PERMANENTLY = 301;

    /**
     * 302 Found
     * 表示请求的资源被暂时的移动到了由该HTTP响应的响应头Location
     * 指定的URL上.
     * 此定义被保留用于向后兼容.SC_FOUND是首选的定义.
     */
    public static final int SC_MOVED_TEMPORARILY = 302;

    /**
    * 302 Found
    * 表示302状态码，推荐使用此常量.
    */
    public static final int SC_FOUND = 302;

    /**
     * 303 See Other
     * 表示对请求的响应可以在另一个URI中被发现.
     */
    public static final int SC_SEE_OTHER = 303;

    /**
     * 304 Not Modified
     * 表示无需再次传输请求的内容，也就是可以使用缓存的内容.
     * 例如GET或HEAD的请求头附带了If-None-Match或If-Modifed-Since的条件头部.
     */
    public static final int SC_NOT_MODIFIED = 304;

    /**
     * 305 Use Proxy 
     * 已弃用.指示请求的资源必须被Location字段指定的代理访问.
     */
    public static final int SC_USE_PROXY = 305;

     /**
     * 307 Temporary Redirect
     * 服务器发送此响应，以指示客户端使用在前一个请求中使用的相
     * 同方法在另一个 URI 上获取所请求的资源.
     */
    public static final int SC_TEMPORARY_REDIRECT = 307;

    /**
     * 400 Bad Request
     * 客户端错误(错误的请求语法、无效的请求消息帧或欺骗性的请求路由).
     */
    public static final int SC_BAD_REQUEST = 400;

    /**
     * 401 Unauthorized 
     * 客户端必须对自身进行身份验证才能获得请求的响应.
     */
    public static final int SC_UNAUTHORIZED = 401;

    /**
     * 402 Payment Required
     * 保留供将来使用.
     */
    public static final int SC_PAYMENT_REQUIRED = 402;

    /**
     * 403 Forbidden
     * 客户端没有访问内容的权限.与401 Unauthorized不同，服务器
     * 知道客户端的身份.
     */
    public static final int SC_FORBIDDEN = 403;

    /**
     * 404 Not Found
     * 服务器找不到请求的资源.
     */
    public static final int SC_NOT_FOUND = 404;

    /**
     * 405 Method Not Allowed
     * 服务器知道请求方法，但目标资源不支持该方法.
     */
    public static final int SC_METHOD_NOT_ALLOWED = 405;

    /**
     * 406 Not Acceptable
     * 表示客户端错误，指代服务器端无法提供与 Accept-Charset 以及 
     * Accept-Language 消息头指定的值相匹配的响应.
     */
    public static final int SC_NOT_ACCEPTABLE = 406;

    /**
     * 407 Proxy Authentication Required
     * 客户端认证需要由代理完成.
     */
    public static final int SC_PROXY_AUTHENTICATION_REQUIRED = 407;

    /**
     * 408 Request Timeout
     * 响应状态码意味着服务器想要关闭这个未使用的连接.
     * 它由一些服务器在空闲连接上发送，即使客户端没有任何先前的请求.
     */
    public static final int SC_REQUEST_TIMEOUT = 408;

    /**
     * 409 Conflict
     * 当请求与服务器的当前状态冲突时，将发送此响应.
     */
    public static final int SC_CONFLICT = 409;

    /**
     * 410 Gone
     * 当请求的内容已从服务器中永久删除且没有转发地址时，
     * 将发送此响应。客户端需要删除缓存和指向资源的链接.
     */
    public static final int SC_GONE = 410;

    /**
     * 411 Length Required
     * 服务端拒绝该请求因为 Content-Length 头部字段未定义但是服务端需要它.
     */
    public static final int SC_LENGTH_REQUIRED = 411;

    /**
     * 412 Precondition Failed
     * 客户端在其头文件中指出了服务器不满足的先决条件.
     */
    public static final int SC_PRECONDITION_FAILED = 412;

    /**
     * 413 Payload Too Large请求实体大于服务器定义的限制.
     * 服务器可能会关闭连接，或在标头字段后返回重试 Retry-After
     */
    public static final int SC_REQUEST_ENTITY_TOO_LARGE = 413;

    /**
     * 414 URI Too Long
     * 客户端请求的 URI 比服务器愿意接收的长度长.
     */
    public static final int SC_REQUEST_URI_TOO_LONG = 414;

    /**
     * 415 Unsupported Media Type
     * 服务器不支持请求数据的媒体格式，因此服务器拒绝请求.
     */
    public static final int SC_UNSUPPORTED_MEDIA_TYPE = 415;

    /**
     * 416 Range Not Satisfiable
     * 无法满足请求中 Range 标头字段指定的范围.该范围可能超出了目标 URI 数据的大小.
     */
    public static final int SC_REQUESTED_RANGE_NOT_SATISFIABLE = 416;

    /**
     * 417 Expectation Failed
     * 此响应代码表示服务器无法满足 Expect 请求标头字段所指示的期望.
     */
    public static final int SC_EXPECTATION_FAILED = 417;

    /**
     * 500 Internal Server Error
     * 服务器遇到了不知道如何处理的情况.
     */
    public static final int SC_INTERNAL_SERVER_ERROR = 500;

    /**
     * 501 Not Implemented
     * 服务器不支持此功能.
     */
    public static final int SC_NOT_IMPLEMENTED = 501;

    /**
     * 502 Bad Gateway
     * 此错误响应表明服务器作为网关需要得到一个处理这个请求的响应，
     * 但是得到一个错误的响应.
     */
    public static final int SC_BAD_GATEWAY = 502;

    /**
     * 503 Service Unavailable
     * 表明服务因维护或重载而停机，无法处理请求.
     */
    public static final int SC_SERVICE_UNAVAILABLE = 503;

    /**
     * 504 Gateway Timeout
     * 当服务器充当网关且无法及时获得响应时，会给出此错误响应
     */
    public static final int SC_GATEWAY_TIMEOUT = 504;

    /**
     * 505 HTTP Version Not Supported
     * 服务器不支持或者拒绝支持请求中使用的HTTP版本.
     */
    public static final int SC_HTTP_VERSION_NOT_SUPPORTED = 505;
}
```