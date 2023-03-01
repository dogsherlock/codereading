##### javax.servlet.SessionCookieConfig
```java
package javax.servlet;

/**
 * 可以通过 ServletContext#getSessionCookieConfig 获得 SessionCookieConfig 实例
 * SessionCookieConfig 可配置不同的 cookie 属性用于会话跟踪
 * 服务器使用Set-Cookie响应头部向用户发送 Cookie 信息,形如
 * Set-Cookie: <cookie-name>=<cookie-value>; [<name>=<value>]...
 * RFC6265规定使用分号和空格间隔key=value,但是有些用户客户端可能不遵守
 */
public interface SessionCookieConfig {

    /**
     * 
     * 设置会话跟踪cookie的名字,默认JSESSIONID
     */
    public void setName(String name);


    /**
     * 获取会话跟踪cookies的名字,默认是JESSIONID
     */
    public String getName();


    /**
     * 设置cookie domain
     * Domain和Path表示定义了Cokkie的作用域，即允许Cookie应该发送给哪些URL
     * Domain指定了哪些主机可以接受Cookie,默认是同一个host不包含子域名
     * eg: Domain=mozilla.org,则Cookie也包含在子域名中developer.mozila.org
     */
    public void setDomain(String domain);


    /**
     * 获取domain属性
     */
    public String getDomain();


    /**
     * path属性指定了一个URL路径，该路径必须存在于请求的URL中，以便发送Cookie标头.
     * 并且子路径也会被匹配
     * eg:
     * 设置path=/docs,则以下地址都会匹配
     * /docs
     * /docs/
     * /docs/web/
     */
    public void setPath(String path);


    /**
     * 获取path属性，默认是ServletContext的上下文路径
     */
    public String getPath();


    /**
     * 获取comment属性,因为Cookie可能包含其它有关用户私有的信息.这个属性允许服务器说
     * 明这个Cookie的使用,用户可以检查这个消息,然话决定是否加入或继续会话.
     */
    public void setComment(String comment);


    /**
     * 获取comment属性
     */
    public String getComment();


    /**
     * eg: Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT; Secure; HttpOnly
     * js document.cookie api无法访问带有HttpOnly属性的cookie,有助于缓解xss跨站脚本攻击
     */
    public void setHttpOnly(boolean httpOnly);


    /**
     * 是否HttpOnly
     */
    public boolean isHttpOnly();


    /**
     *
     * Secure属性的Cookie只应通过被HTTPS协议加密过的请求发送给服务端,永远不会使用
     * 不安全的HTTP发送(本地主机除外),但不会阻止对cookie中敏感信息的访问
     */
    public void setSecure(boolean secure);


    /**
     * 是否cookies有secure属性
     */
    public boolean isSecure();


    /**
     * 以秒为单位设置cookie存活时间
     * cookie失效时刻=创建时刻+max-age
     */
    public void setMaxAge(int maxAge);


    /**
     * 获取max-age存活时间
     */
    public int getMaxAge();
}
```