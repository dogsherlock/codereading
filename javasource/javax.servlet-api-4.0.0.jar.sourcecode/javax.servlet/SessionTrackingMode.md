##### javax.servlet.SessionTrackingMode
```java
package javax.servlet;

/**
 * 会话跟踪模式的枚举类型
 * 1. COOKIE
 * 服务器创建保存于浏览器端，不可跨域名性，大小及数量有限，每次浏览器都会请求头
 * 中携带COOKIE来保持会话状态
 * 2. URL 
 * 将用户session的id信息重写到URL地址中，以便在服务器端进行识别不同的用户.URL重
 * 写能够在客户端停用cookie或者不支持cookie的时候发挥作用
 * 3. SSL
 * 支持安全传输协议的会话交互
 */
public enum SessionTrackingMode {
    COOKIE,
    URL,
    SSL
}
```