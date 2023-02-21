##### javax.servlet.http.MappingMatch
```java
package javax.servlet.http;

/**
 * Servlet 映射枚举类型
 * <p>Enumeration of Servlet mapping types.</p>
 */
public enum MappingMatch {
    /**
     * 根目录
     */
    CONTEXT_ROOT,
    /**
     * 默认路径/
     */
    DEFAULT,
    /**
     * 精准匹配
     */
    EXACT,
    /**
     * 扩展
     */
    EXTENSION,
    /**
     * 路径
     */
    PATH
}
```