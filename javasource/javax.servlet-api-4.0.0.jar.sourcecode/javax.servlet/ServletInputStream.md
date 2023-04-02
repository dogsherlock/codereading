##### javax.servlet.ServletInputStream
```java
package javax.servlet;

import java.io.InputStream;
import java.io.IOException;

/**
 * 提供从客户端请求中读取二进制数据的输入流，包含一个高效的方法readLine
 * 来每次读取一行数据. 在一些协议中，如HTTP POST和PUT method中，
 * ServletInputStream对象可以被用来读取客户端发送的数据.
 *
 * 可以通过ServletRequest#getInputStream方法获取ServletInputStream对象
 *
 * servlet容器需要实现ServletInputStream这个抽象类.其子类需要实现
 * java.io.InputStream.read()方法.
 */
public abstract class ServletInputStream extends InputStream {

    /**
     * 这是一个抽象类，此构造函数什么都不做
     */
    protected ServletInputStream() { }
    
    /**
     * 每次从输入流中读取一行.读取字节流向偏移量为offset处的数组，知道
     * 读取一定量的字节或者遇到了换行符.也会将换行符读入数组中.
     *
     * 如果在读取到最大数量的字节之前，到达了输入流的结尾，此方法返回-1
     *
     * @param b 读取字节流后存储的数组
     *
     * @param off 从数组的offset开始存储读取的字节流	
     *
     * @param len len表示最大读取的字节数
     *
     * @return 返回实际读取字节的数量，如果流读取结束则返回-1
     *
     * @exception IOException	I/O Exception
     */
    public int readLine(byte[] b, int off, int len) throws IOException {
	if (len <= 0) {
	    return 0;
	}
	int count = 0, c;

    /* 
    使用read()循环读取，将读取内容赋值给array b[off++]，同时count记录读取字节长度 
    判断count是否到达最大读取的字节数len，如果是或者读取了换行符，则停止.返回读取字节数count.
    */
	while ((c = read()) != -1) {
	    b[off++] = (byte)c;
	    count++; 
	    if (c == '\n' || count == len) {
		break;
	    }
	}
	return count > 0 ? count : -1;
    }

    /**
     * 当流中所有的数据被读取返回true，否则返回false
     */
    public abstract boolean isFinished();

    /**
     * 如果数据可以直接读取返回true，否则返回false
     */
    public abstract boolean isReady();

    /**
     * 当数据可读，触发注册的ReaderListener监听器
     *
     * @exception IllegalStateException 如果下列条件之一成立
     * <ul>
     * <li> 关联的请求没有升级或者没有开启异步
     * <li> 在相同的request范围多次调用setReadListener
     * </ul>
     *
     * @throws NullPointerException readListener is null
     */
    public abstract void setReadListener(ReadListener readListener);
}
```