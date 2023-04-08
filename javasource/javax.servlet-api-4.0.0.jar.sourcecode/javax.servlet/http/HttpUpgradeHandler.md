##### javax.servlet.http.HttpUpgradeHandler
```java
package javax.servlet.http;

/**
 * 用于包裹协议升级处理的接口.
 */
public interface HttpUpgradeHandler {
    /**
     * 协议升级完成，并且升级后的连接准备使用新的协议时调用一次.
     *
     * @param wc 与升级的请求关联的WebConnection obj. 
     */
    public void init(WebConnection wc);

    /**
     * 当客户端断开连接被调用
     */
    public void destroy();
}
```