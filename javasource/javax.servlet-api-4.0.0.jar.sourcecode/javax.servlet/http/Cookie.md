##### javax.servlet.http.Cookie
**HTTP State Management Mechanism**
* [netscape cookie spec](https://www.rfc-editor.org/rfc/rfc6265#ref-Netscape)
* [rfc2109](https://www.rfc-editor.org/rfc/rfc2109)
* [rfc2965](https://www.rfc-editor.org/rfc/rfc2965)
* [rfc6265](https://www.rfc-editor.org/rfc/rfc6265)

___

```java
package javax.servlet.http;

import java.io.Serializable;
import java.text.MessageFormat;
import java.util.Locale;
import java.util.ResourceBundle;

/**
 * 创建一个cookie，Servlet发送少量的数据到web浏览器，由浏览器保存，稍后
 * 发送回服务器.
 * cookie的值可以唯一标识客户端，所以cookie通常用于会话管理.
 * 
 * cookie有一个name/value和可选属性(如注释，Cookie的路径作用域，domain作用域，
 * cookie有效时间，版本号version0/version1).一些web浏览器在处理可选属性的方式
 * 上存在缺陷，因此要谨慎使用它们来提供servlet的互操作性.
 *
 * servlet使用HttpServletResponse#addCookie方法将cookie发送到浏览器，
 * 该方法向Http响应头添加字段Set-Cookie字段，以便一次发送一个cookie
 * 到浏览器.浏览器预计支持每个web服务器20个cookie，总共300个cookie，
 * 并且每个cookie大小限制为4kb.
 * 浏览器通过向HTTP请求头添加字段Cookie将cookies返回给servlet.可以使用
 * HttpServletRequest#getCookies方法获取请求中的cookies.多个cookie也许
 * 可能有相同的name,但路径属性不同.
 * 
 * cookie会影响使用它们的网页的缓存，HTTP1.0不缓存使用此类创建的cookie的页面，
 * 此类不支持HTTP1.1定义的缓存控制(Cache-Control头).
 *
 * 此类同时支持version0(netscape)和version1(rfc 2109)的cookie规范.默认情况下，
 * cookie使用version0创建的，以确保最佳互操作性.
 */
public class Cookie implements Cloneable, Serializable {
    /**
     * implements Serializable
     */ 
    private static final long serialVersionUID = -6454587001725327448L;
    
    private static final String TSPECIALS;

    /**
     * base name of resource bundle
     */
    private static final String LSTRING_FILE =
        "javax.servlet.http.LocalStrings";

    /**
     * 错误信息国际化
     */
    private static ResourceBundle lStrings =
        ResourceBundle.getBundle(LSTRING_FILE);

    // 类加载后初始化TSPECIALS
    static {
        if (Boolean.valueOf(System.getProperty("org.glassfish.web.rfc2109_cookie_names_enforced", "true"))) {
            TSPECIALS = "/()<>@,;:\\\"[]?={} \t";
        } else {
            TSPECIALS = ",; ";
        }
    }
    
    /**
     * cookie name
     */
    private String name;

    /**
     * cookie value
     */
    private String value;

    /**
     * 可选，描述cookies的用途
     */
    private String comment;

    /**
     * cookie在指定domain下有效,如果指定了domain，则一般包含子域名.
     * 如果不指定，该属性默认为同一host设置cookie
     */
    private String domain;

    /**
     * Max-Age=delta-seconds. cookie的存活时间(delta-seconds之后)
     */
    private int maxAge = -1;

    /**
     * cookie生效所在的URL子集
     */
    private String path;

    /**
     * Secure没有value，表示使用SSL等安全协议
     */
    private boolean secure;

    /**
     * cookie遵循的状态管理spec，version1表示遵循rfc 2109++风格
     */
    private int version = 0;

    /**
     * js脚本无法读取到cookie信息(document.cookie，可以缓解xss攻击窃取cookie内容)
     */
    private boolean isHttpOnly = false;

    /**
     * 用给定的name和value构造一个cookie obj.
     *
     * name必须遵循RFC2109规范(以$开头的name被保留做其他用途，不能被应用使用；
     * value必须是可打印的ASCII编码，具体见rfc2109 spec)，然而厂商可能会提供
     * 配置选项来允许cookie遵循最初的netscape的cookie规范.当cookie被创建，
     * 不能修改cookie的name.可以通过#setValue来改变cookie的value.
     *
     * 默认情况下，cookie是根据netscape cookie规范创建的，可以使用#setVersion方法
     * 改变.
     * 
     * cookie name需要遵循: 
     * 非Java保留标记，不区分大小写情况下，不等于Comment、Domain、Expires、Max-Age、
     * Path、Secure、Version、不以$开头.
     * 
     * @throws IllegalArgumentException	如果cookie name为null，空字符串或者含有
     * 如逗号、分号、空格等非法字符，或含有cookie协议中保留字符.
     */
    public Cookie(String name, String value) {
        if (name == null || name.length() == 0) {
            throw new IllegalArgumentException(
                    // Cookie name must not be null or empty
                    lStrings.getString("err.cookie_name_blank"));
        }
        // token是对编译器有意义的最小单元，如关键字、标识符、常量、特殊符号、运算符等
        if (!isToken(name) ||
                name.equalsIgnoreCase("Comment") || // rfc2109
                name.equalsIgnoreCase("Discard") || // 2019++
                name.equalsIgnoreCase("Domain") ||
                name.equalsIgnoreCase("Expires") || // (old netscape cookies)
                name.equalsIgnoreCase("Max-Age") || // rfc2109
                name.equalsIgnoreCase("Path") ||
                name.equalsIgnoreCase("Secure") ||
                name.equalsIgnoreCase("Version") ||
                name.startsWith("$")) {
            String errMsg = lStrings.getString("err.cookie_name_is_token");
            Object[] errArgs = new Object[1];
            errArgs[0] = name;
            errMsg = MessageFormat.format(errMsg, errArgs);
            // errMsg: Cookie name \"name\" is a reserved token
            throw new IllegalArgumentException(errMsg);
        }

        this.name = name;
        this.value = value;
    }

    /**
     * 设置comment，netscape cookie version0不支持.
     */
    public void setComment(String purpose) {
        comment = purpose;
    }

    /**
     * 获取comment
     */ 
    public String getComment() {
        return comment;
    }
    
    /**
     * 设置domain
     * domain名格式由rfc2019指定，以.开始(如.foo.com，表示对www.foo.com可见，
     * 但对a.b.foo.com不可见).默认情况下，cookie只会返回给发送给它的服务器(host).
     */
    public void setDomain(String domain) {
        // 根据Locale语言为英语进行小写转换
        this.domain = domain.toLowerCase(Locale.ENGLISH); // IE allegedly needs this
    }

    /**
     * 获取domain
     */ 
    public String getDomain() {
        return domain;
    }

    /**
     * 设置cookie的有效时间(秒)
     *
     * 正值表示cookie将会在delta-seconds后过期.负值表示cookie不会持久储存，将
     * 在浏览器退出后删除.0值将导致cookie被删除.
     */
    public void setMaxAge(int expiry) {
        maxAge = expiry;
    }

    /**
     * 获取Max-Age
     *
     * 默认情况下，返回-1，表示cookie在浏览器关闭之后将删除
     */
    public int getMaxAge() {
        return maxAge;
    }
    
    /**
     * 设置path
     *
     * cookie在设定的目录和子目录下是可见的.cookie的path必须包括servlet设置
     * cookie的path. 
     * eg. /catalog/将会使cookie对于此服务/catalog下的所有目录可见
     */
    public void setPath(String uri) {
        path = uri;
    }

    /**
     * 获取path
     */ 
    public String getPath() {
        return path;
    }

    /**
     * 向浏览器说明是否应该使用安全协议(如HTTPS或者SSL)发送cookie.
     *
     * 默认值为false
     */
    public void setSecure(boolean flag) {
        secure = flag;
    }

    /**
     * 获取secure的值
     */
    public boolean getSecure() {
        return secure;
    }

    /**
     * 获取cookie name，创建之后不能改变name.
     */
    public String getName() {
        return name;
    }

    /**
     * 设置value
     * 
     * 如果使用二进制值，可以使用base64进行编码成字符串
     *
     * 对于version 0的cookie，值必须不含空格，花括号，圆括号，等于号，逗号
     * ，双引号，斜杠，问号，@，冒号，分号.空值可能在不同浏览器上有不同的含义.
     */
    public void setValue(String newValue) {
        value = newValue;
    }

    /**
     * 获取value
     */
    public String getValue() {
        return value;
    }

    /**
     * 获取cookie遵从的协议版本.
     * 1表示rfc2109，0表示netscape cookie协议.
     */
    public int getVersion() {
        return version;
    }

    /**
     * 设置version
     */
    public void setVersion(int v) {
        version = v;
    }

    /*
     * 测试给定字符串是否是java语言中的保留token
     */
    private boolean isToken(String value) {
        int len = value.length();
        for (int i = 0; i < len; i++) {
            char c = value.charAt(i);
            /*
            ASCII码表中前32个字符是不能用于打印的控制字符.第127个字符是删除命令.
            在TSPECIALS特殊字符中
            以上两种情况之内的字符都不是保留token，返回false.
             */
            if (c < 0x20 || c >= 0x7f || TSPECIALS.indexOf(c) != -1) {
                return false;
            }
        }

        return true;
    }

    /**
     * 重写Object类的clone方法来返回cookie的副本(浅拷贝，但是cookie的相关属性都是
     * 基本类型，没有引用类型可以做到完全copy.)
     */
    public Object clone() {
        try {
            return super.clone();
        // 没有实现Cloneable接口会抛出CloneNotSupportedException异常
        } catch (CloneNotSupportedException e) {
            throw new RuntimeException(e.getMessage());
        }
    }

    /**
     * 设置HttpOnly的值
     *
     * 如果设置为true，则cookie会有HttpOnly标记.
     *
     * HttpOnly标记的cookie不应该被暴露在客户端的js代码中，可以缓和xxs攻击.
     */
    public void setHttpOnly(boolean isHttpOnly) {
        this.isHttpOnly = isHttpOnly;
    }
 
    /**
     * 检查cookie是否有HttpOnly标记
     */
    public boolean isHttpOnly() {
        return isHttpOnly;
    }
}
```