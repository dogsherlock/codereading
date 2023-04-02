##### javax.servlet.ServletOutputStream
```java
package javax.servlet;

import java.io.OutputStream;
import java.io.IOException;
import java.io.CharConversionException;
import java.text.MessageFormat;
import java.util.ResourceBundle;

/**
 * 为向客户端发送二进制数据提供一个输出流.可以通过ServletResponse#getOutputStream
 * 方法获取ServletOutputStream对象.
 *
 * ServletOutputStream是一个抽象类.由servlet容器实现其子类，这个类的子类必须实现
 * java.io.OutputStream.write(int)方法.
 */
public abstract class ServletOutputStream extends OutputStream  {
    /* 根据地区读取自定义名_语言代码_国别代码.properties文件 */
    private static final String LSTRING_FILE = "javax.servlet.LocalStrings";
    private static ResourceBundle lStrings =
        ResourceBundle.getBundle(LSTRING_FILE);
    
    /**
     * 无参构造函数，不做任何事
     */
    protected ServletOutputStream() { }

    /**
     * 将字符串写入客户端，末尾没有回车换行(CRLF)字符
     *
     * @exception IOException I/O Exception
     */
    public void print(String s) throws IOException {
        // s为null写入null字符串
        if (s==null) s="null";
        int len = s.length();
        for (int i = 0; i < len; i++) {
            char c = s.charAt (i);

            /*
             这是和当前servlet框架一致的唯一方法，但显然对于很多字符串是不正确的.
             字符串需要满足servlet输出流可以编码输出
             高位字节必须是0，否则抛出CharConversionException异常
             */
            if ((c & 0xff00) != 0) {  
                // errMsg: Not an ISO 8859-1 character: {0}
                // ISO-8859-1编码是单字节编码，向下兼容ASCII，其编码范围是0x00-0xFF
                String errMsg = lStrings.getString("err.not_iso8859_1");
                Object[] errArgs = new Object[1];
                errArgs[0] = Character.valueOf(c);
                // 字符串格式化 Not an ISO 8859-1 character: c
                errMsg = MessageFormat.format(errMsg, errArgs);
                throw new CharConversionException(errMsg);
            }
            write (c);
        }
    }

    /**
     * 将boolean值写入客户端，末尾无回车换行符
     *
     * @exception IOException I/O Exception
     */
    public void print(boolean b) throws IOException {
        String msg;
        if (b) {
            // msg = "true"
            msg = lStrings.getString("value.true");
        } else {
            // msg = "false"
            msg = lStrings.getString("value.false");
        }
        print(msg);
    }

    /**
     * 将字符写入客户端，末尾无回车换行符
     *
     * @exception IOException I/O Exception
     *
     */
    public void print(char c) throws IOException {
        print(String.valueOf(c));
    }

    /**
     * 将int写入客户端，末尾无回车换行符
     *
     * @exception IOException I/O Exception
     *
     */  
    public void print(int i) throws IOException {
        print(String.valueOf(i));
    }
 
    /**
     * 将long写入客户端，末尾无回车换行符
     *
     * @exception IOException I/O Exception
     *
     */ 
    public void print(long l) throws IOException {
        print(String.valueOf(l));
    }

    /**
     * 将float写入客户端，末尾无回车换行符
     *
     * @exception IOException I/O Exception
     *
     */ 
    public void print(float f) throws IOException {
        print(String.valueOf(f));
    }

    /**
     * 将double写入客户端，末尾无回车换行符
     *
     * @exception IOException I/O Exception
     *
     */ 
    public void print(double d) throws IOException {
        print(String.valueOf(d));
    }

    /**
     * 将回车换行符(CRLF，即"\r\n")写入客户端
     *
     * @exception IOException I/O Exception
     *
     */
    public void println() throws IOException {
        print("\r\n");
    }

    /**
     * 将字符串写入客户端，末尾带有回车换行(CRLF)字符
     *
     * @exception IOException I/O Exception
     */
    public void println(String s) throws IOException {
        print(s);
        println();
    }

    /**
     * 将boolean值写入客户端，末尾带有回车换行符
     *
     * @exception IOException I/O Exception
     */
    public void println(boolean b) throws IOException {
        print(b);
        println();
    }

    /**
     * 将char值写入客户端，末尾带有回车换行符
     *
     * @exception IOException I/O Exception
     */
    public void println(char c) throws IOException {
        print(c);
        println();
    }

    /**
     * 将int值写入客户端，末尾带有回车换行符
     *
     * @exception IOException I/O Exception
     */
    public void println(int i) throws IOException {
        print(i);
        println();
    }

    /**
     * 将long值写入客户端，末尾带有回车换行符
     *
     * @exception IOException I/O Exception
     */
    public void println(long l) throws IOException {
        print(l);
        println();
    }

    /**
     * 将float值写入客户端，末尾带有回车换行符
     *
     * @exception IOException I/O Exception
     */
    public void println(float f) throws IOException {
        print(f);
        println();
    }

    /**
     * 将double值写入客户端，末尾带有回车换行符
     *
     * @exception IOException I/O Exception
     */
    public void println(double d) throws IOException {
        print(d);
        println();
    }

    /**
     * 返回数据是否可以直接被写入
     */
    public abstract boolean isReady();

    /**
     * 当可以直接写入数据时，ServletOutputStream可以调用WriterListener
     *
     * @exception IllegalStateException 如果下列条件之一成立
     * <ul>
     * <li> 关联的请求没有升级或者没有开启异步
     * <li> 在相同的request范围多次调用setWriteListener
     *
     * @throws NullPointerException writeListener is null
     */
    public abstract void setWriteListener(WriteListener writeListener);
}
```