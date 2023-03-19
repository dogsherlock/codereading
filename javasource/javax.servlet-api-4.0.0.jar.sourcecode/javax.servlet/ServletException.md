##### javax.servlet.ServletException
```java
package javax.servlet;


/**
 * 定义一个servlet通用异常
 */
public class ServletException extends Exception {

    private Throwable rootCause;

    /**
     * 实例化一个新的servlet异常
     */
    public ServletException() {
	super();
    }

    /**
     * 使用message构造新的servelt异常
     */
    public ServletException(String message) {
	super(message);
    }
    
    /**
     * 同上
     */
    public ServletException(String message, Throwable rootCause) {
	super(message, rootCause);
	this.rootCause = rootCause;
    }

    /**
     * message是调用rootCause的getLocalizedMessage方法
     */
    public ServletException(Throwable rootCause) {
	super(rootCause);
	this.rootCause = rootCause;
    }
  
    /**
     * 返回导致servlet异常的异常
     */
    public Throwable getRootCause() {
	return rootCause;
    }
}
```