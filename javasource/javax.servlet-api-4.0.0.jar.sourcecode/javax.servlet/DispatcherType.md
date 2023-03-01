##### javax.servlet.DispatcherType
```java
package javax.servlet;

/**
 * 过滤器的转发模式枚举类
 */
public enum DispatcherType {
    // 转发访问资源
    FORWARD,
    // 包含访问资源
    INCLUDE,
    // 只在请求服务器的时候才发生过滤，如果是服务器内部请求转发的话是不会被过滤的
    REQUEST,
    // 异步访问资源
    ASYNC,
    // 错误跳转资源
    ERROR
}
```