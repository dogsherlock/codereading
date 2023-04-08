##### javax.servlet.http.PushBuilder
```java
package javax.servlet.http;

import java.util.Set;

import javax.servlet.http.Cookie;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

/** 
 * 构建一个要推送的请求.
 * 
 * 根据RFC 7540(HTTP/2) 8.2部分，一个承诺的请求必须是可缓存的、安全的、没有请求体.
 *
 * 可以通过HttpServletRequest#newPushBuilder来获取一个新的PushBuilder对象(null).
 * 在返回的PushBuilder上的改变不会反映到未来的返回.
 *
 * <p>按照以下顺序初始化此实例:</p>
 *
 * <ul>
 * <li>请求方法为"GET"</li>
 *
 * <li> 当前的HttpServletRequest对象的请求头被加入到PushBuilder，除了:
 *   <li>条件首部，如If-Modified-Since (RFC 7232)
 * <ul>
 *   <li>范围首部(如Range、If-Range)
 *   <li>Expect首部(Expect: 100-continue)
 *   <li>协商首部(如Accept-Encoding、Accept-Language)
 *   <li>Referer首部(包含了当前请求页面的来源页面的地址)
 * </ul>
 * </li>
 *
 * <li> 如果一个请求被认证的，那么Authorizaiton头将会被设置为容器生成的token，
 * 推送的请求的Authorization和其相同.
 *
 * sessionid是HttpServletRequest#getRequestedSessionId返回的值.如果之前
 * 调用过HttpServletRequest#getSession(boolean)来创建新的session，则PushBuilder
 * 的请求sessionid为此新的sessionid.
 * sessionid有两个来源: cookie或者URL(可以通过HttpServletRequest#
 * isRequestedSessionIdFromCookie和HttpServletRequest#isRequestedSessionIdFromURL判别)
 * PushBuilder的sessionid的来源相同.
 *
 * <li> Referer头将会被设置为HttpServletRequest#getRequestURL加上
 * 任何HttpServletRequest#getQueryString.</li>
 *
 * <li> 如果在关联的response对象上已经调用了HttpServletResponse#addCookie(Cookie)，
 * 那么相关的Cookie头会被加入到PushBuilder对象，除非Cookie#getMaxAge方法<=0，在这种
 * 情况下cookie将会从builder中移除.</li>
 * </ul> 
 *
 * <p> 在调用#push方法之前必须调用PushBuilder实例的#path方法.否则，调用
 * #push方法会造成异常.</p>
 * 
 * 在调用#push方法之前，可以通过链式调用来定制一个PushBuilder，来实例化
 * 一个异步推送请求.builder可以重新使用#push方法，然而调用#push之前#path(String)
 * 和条件首部的值必须被清除.其它的值在调用中保留.
 */
public interface PushBuilder {
    /** 
     * <p>设置用来推送的方法</p>
     * 
     * @throws NullPointerException method为null
     *
     * @throws IllegalArgumentException  如果参数是空字符串，或者包含任何
     * 不可缓存或者不安全的方法(RFC 7231)，如POST、PUT、DELETE、CONNECT、OPTIONS和TRACE方法
     * 
     * 说明:
     * HTTP METHOD: CONNECT DELETE GET HEAD OPTIONS PATCH POST PUT TRACE
     * 可缓存的方法:  GET HEAD POST
     * 不安全的方法: CONNECT DELETE POST PUT PATCH TRACE 
     */
    public PushBuilder method(String method);
    
    /**
     * 设置用于推送的查询字符串
     *
     * 此查询字符串将会被添加到调用#path(String)的任意查询字符串. 比如保留
     * 任何重复则参数.
     * 如果使用具有相同查询字符串的多个#push调用，应该使用此方法而不是#path(String).
     */
    public PushBuilder queryString(String queryString);
    
    /**
     * 设置用于推送的sessionid
     * sessionid将以与关联请求相同的方式设置(与请求关联的cookie或者url参数).
     * 默认是请求的sessionid或者是新创建的session的sessionid.
     */
    public PushBuilder sessionId(String sessionId);
    
    /** 
     * 设置用于推送的请求头，如果已经存在相同的请求头，则会覆盖value.
     */
    public PushBuilder setHeader(String name, String value);
    
    /** 
     * 添加用于推送的请求头.
     */
    public PushBuilder addHeader(String name, String value);

    /** 
     * 移除指定name的请求头，如果不存在，则不做什么.
     */
    public PushBuilder removeHeader(String name);

    /** 
     * 设置用于推送的URL路径.以"/"开头的路径被视作绝对路径，否则是相对于关联
     * 请求上下文你路径的相对路径. 没有默认的路径，并且每次调用#push之前都要调用
     * #path(String).如果path方法的参数包含查询字符串，则需要与#queryString的
     * 向合并，保留重复的内容.
     */
    public PushBuilder path(String path);
    
    /**
     * 给当前状态的builder推送资源，此方法必须是非阻塞的.
     *
     * 调用此方法不保证资源将实际被推送，因为客户端可以使用底层的HTTP/2协议
     * 拒绝推送的资源.
     *
     * 如果builder拥有sessionid，则推送的请求也会在cookie或URI参数中包含sessionid.
     *
     * 在此方法返回之前，其路径、条件首部会被清除，其它保留的字段可能会在另一个推送中
     * 重新使用.
     *
     * @throws IllegalStateException 在builder实例化和未抛出IllegalStateException异常
     * 之间没有调用#path
     */
    public void push();
    
    /**
     * 返回用于推送的方法.
     */
    public String getMethod();

    /**
     * 返回用于推送的查询字符串.
     */
    public String getQueryString();

    /**
     * 返回用于推送的sessionid
     */
    public String getSessionId();

    /**
     * 返回用于推送的首部集合.
     *
     * 修改返回的set不会影响PushBuilder对象.
     */
    public Set<String> getHeaderNames();

    /**
     * 返回用于推送的给定name的首部的value.
     */
    public String getHeader(String name);

    /**
     * 返回用于推送的URL路径.
     */
    public String getPath();
}
```