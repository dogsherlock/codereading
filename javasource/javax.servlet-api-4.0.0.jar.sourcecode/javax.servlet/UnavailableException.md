##### javax.servlet.UnavailableException
```java
package javax.servlet;


/**
 * 定义servlet或filter抛出的异常，表示永久或者暂时不可用.
 */
public class UnavailableException
extends ServletException {

    /**
     * 不可以用的servlet
     */
    private Servlet     servlet;

    /**
     * 是否是永久性错误
     */
    private boolean     permanent;

    /**
     * 不可用估计
     */
    private int         seconds;

    /**
     * @deprecated	到Servlet API 2.2为止，使用#UnavailableException(String)代替. 
     */
    @Deprecated
    public UnavailableException(Servlet servlet, String msg) {
        super(msg);
        this.servlet = servlet;
        permanent = true;
    }
 
    /**
     * @deprecated	到Servlet API 2.2为止，到Servlet API 2.2为止，
     * 使用#UnavailableException(String, int)代替.
     *
     * @param seconds 指定servlet预期不可用的时间(秒)，如果为0或负数，表明不可以估计.
     *
     * @param servlet 不可用的servlet
     * 
     * @param msg 指定异常的描述性信息，可以被写入日志文件或者展示给用户.
     */
    @Deprecated
    public UnavailableException(int seconds, Servlet servlet, String msg) {
        super(msg);
        this.servlet = servlet;
        if (seconds <= 0)
            this.seconds = -1;
        else
            this.seconds = seconds;
        permanent = false;
    }

    /**
     * 传入描述信息的构造器，表明servlet永久不可用.
     */
    public UnavailableException(String msg) {
        super(msg);

        permanent = true;
    }

    /**
     * 传入传入描述信息的构造器，表明servlet暂时不可用，并传入不可用的估计时间.
     */
    public UnavailableException(String msg, int seconds) {
        super(msg);

        if (seconds <= 0)
            this.seconds = -1;
        else
            this.seconds = seconds;

        permanent = false;
    }

    /**
     *
     * 返回servlet是否永久不可用.
     */
    public boolean isPermanent() {
	    return permanent;
    }
  
    /**
     * @deprecated 到Servlet API 2.2为止，没有可替换的.
     *
     * 返回不可用的servlet.
     */
    @Deprecated
    public Servlet getServlet() {
	    return servlet;
    }

    /**
     * 返回servlet预计暂时不可用的秒数.
     *
     * 如果返回-1，则表示servlet暂时不可用，或者无法提供评估时间.
     * 不对异常创建后随着时间流逝对评估时间进行调整.
     */
    public int getUnavailableSeconds() {
	    return permanent ? -1 : seconds;
    }
}
```