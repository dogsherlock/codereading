##### javax.servlet.ServletResponse
```java
package javax.servlet;

import java.io.IOException;
import java.io.PrintWriter;
import java.util.Locale;

/**
 * 定义一个对象来帮助servlet发送response给客户端
 * servlet容器创建一个ServletResponse对象并将其作
 * 为一个参数传递给servlet的service方法
 *
 * 调用#getOutputStream获得ServletOutputStream对象，
 * 发送二进制数据.调用#getWriter获得PrintWriter对象，
 * 发送字符数据.要发送混合的二进制和文本数据，如创建一个
 * multipart响应，使用ServletOutputStream对象并手动
 * 管理字符部分.
 *
 * 响应体的编码类型可以由下面任何方法显示指定: 每个请求、使用
 * ServletContext#setRequestCharacterEncoding、部署描述符、
 * 以及每个容器使用厂商指定的配置(如tomcat, 在Tomcat中，Web
 * 应用的部署描述信息包括$CATALINA BASE/conf/web.xml中的默认配置
 * 以及Web应用WEB-INF/web.xml下的定制配置)，优先级从高到低.
 * 对于每个请求，响应体的编码可以使用#setCharacterEncoding和
 * #setContentType方法明确指定，或使用#setLocale方法隐式指定.
 * 显示指定的优先级更高.
 * 如果没有指定编码类型，则默认使用ISO-8859-1编码. 
 * #setCharacterEncoding、#setContentType、#setLocale方法必须
 * 在调用#getWriter方法和提交response之前使用. 
 */
public interface ServletResponse {
    /**
     * 返回请求体字符编码类型名
     * 推荐使用下列指定请求体字符编码的方式，它们的优先级是降序的.
     * per request、#ServletContext#setResponseCharacterEncoding、
     * deployment discriptor、per container, using vendor specific 
     * configuration.
     */
    public String getCharacterEncoding();
    
    /**
     * 返回响应体使用的内容类型.必须在response提交之前调用
     * #setContentType指定内容类型.如果不指定，则此方法返回
     * null.
     * 如果已经指定了MIME类型和字符编码，此方法返回此字符串.
     * 如果没有指定字符编码，则在返回结果中字符编码会被忽略.
     *
     * @return 表示content type的字符串，如"text/html; charset=UTF-8"
     * 或者null 
     */
    public String getContentType();

    /**
     * 返回一个ServletOutputStream对象来向请求写入二进制数据.Servlet容器
     * 不会将二进制数据编码.
     * 
     * 调用ServletOutputStream对象的flush()提交响应
     * 不能同时使用#getOutputStream或#getWriter来写入请求体，除非调用了
     * #reset方法只有，再去调用另一个.
     *
     * @exception IllegalStateException 如果此对象上已经调用了getWriter方法
     *
     * @exception IOException I/O Exception
     */
    public ServletOutputStream getOutputStream() throws IOException;
    
    /**
     * 返回一个可以向客户端发送字符文本的PrintWriter对象.
     * PrintWriter使用#getCharacterEncoding返回的字符编码.
     * 如果响应体字符编码没有指定，getCharacterEncoding默认返回
     * ISO-8859-1编码.getWriter更新编码为ISO-8859-1.
     * 调用PrintWriter对象的flush()提交响应.
     * 不能连续调用#getOutputStream和#getWriter方法来写入请求体，
     * 除非期间调用了#reset方法
     *
     * @exception java.io.UnsupportedEncodingException 如果
     * 不支持#getChracterEncoding方法返回的字符编码
     *
     * @exception IllegalStateException 在此response对象上
     * 已经调用了#getOutputStream方法
     *
     * @exception IOException I/O Exception
     */
    public PrintWriter getWriter() throws IOException;
    
    /**
     * 设置发送给客户端的response的字符编码类型，如UTF-8.
     * 如果已经使用ServletContext#setResponseCharacterEncoding方法
     * 设置过了response字符编码类型、部署描述符、或使用#setContentType
     * 或#setLocale方法，此方法将覆盖以上所有方式.
     * 调用#setContentType("text/html")以及#setContentType("UTF-8")
     * 相当于调用#setContentType("text/html; charset=UTF-8")
     * 
     * 可以多次调用此方法来改变字符编码.
     * 如果已经调用了#getWriter或已经提交了response，调用此方法没有效果.
     * 如果协议支持，容器必须告知客户端servlet response的字符编码类型.
     * 如HTTP协议，可以通过响应头告知文本类型的编码类型.如果servlet没有
     * 指定一种内容类型，则不指定.
     */
    public void setCharacterEncoding(String charset);
    
    /**
     * 设置返回给客户端的response响应体的字节长度.
     * 在http协议中，此方法设置HTTP响应头Content-Length.
     */
    public void setContentLength(int len);
    
    /**
     * 同上.
     *
     * @param len long指定返回给客户端的响应体的内容字节长度，设置Content-Length头
     */
    public void setContentLengthLong(long len);

    /**
     * 在response提交之前, 设置响应头Content-Type.
     * 参数content type可以包含字符编码，如"text/html; charset=UTF-8".
     * 
     * 可以多次调用此方法改变MIME类型和字符编码.
     */
    public void setContentType(String type);
    
    /**
     * 设置响应体首选的缓冲区大小，servlet容器将会使用大小至少为size的
     * 缓冲区.实际使用的缓冲区大小可以通过#getBufferSize方法得到
     *
     * 在实际写入之前，大的缓冲区允许更多的内容被写入，因此给与servlet更多时间
     * 来设置合适的状态码和响应头.较小的缓冲区减轻了服务器的内存负载，允许客户端
     * 更快地接收数据.
     *
     * 此方法需要在写入响应数据之前调用.如果已经写入了内容，或者response已经被
     * 提交，此方法抛出IllegalStateException异常.
     *
     * @exception IllegalStateException 在写入内容之后调用此方法
     */
    public void setBufferSize(int size);
   
    /**
     * 返回response使用的实际缓冲区大小.如果没有缓冲区被使用，则此方法返回0.
     */
    public int getBufferSize();
    
    /**
     * 将缓冲区的任何内容强制写入客户端.调用此方法会自动提交response，意味着
     * 状态码和响应头会被写入.
     * 
     * @throws IOException 如果刷新缓冲区失败
     */
    public void flushBuffer() throws IOException;
    
    /**
     * 清除response的底层缓冲区的内容，不包括响应头和状态码.如果
     * response已经被提交，此方法抛出IllegalStateException异常
     */
    public void resetBuffer();
    
    /**
     * 检查response是否已经被提交.一个被提交的response已经有
     * 请求头和响应头写入.
     */
    public boolean isCommitted();
    
    /**
     * 清除缓冲区的数据、状态码以及响应头. 调用#getWriter或#getOutputStream
     * 的状态也被清除.调用#getWriter后，调用#reset方法然后调用#getInputStream
     * 方法是合法的.如果response已经被提交，此方法抛出IllegalStateException.
     *
     * @exception IllegalStateException  response已经被提交
     */
    public void reset();
    
    /**
     * 如果response没有被提交，设置response的语言区域.
     * 如果没有使用#setContentType或#setCharacterEncoding设置字符编码、
     * 没有调用#getWriter、response没有被提交，可以设置地区的response
     * 字符编码.
     * 
     * 如果部署描述符包含<locale-encoding-mapping-list>标签，此标签
     * 用于指定本地化与响应编码的映射关系.否则，本地化与编码的对应关系
     * 是依赖于容器的(如tomcat web.xml).
     * 可以多次调用此方法来改变locale.如果response已经提交，调用此方法
     * 无效果.调用#setContentType指定charset、调用#setCharacterEncoding、
     * 调用#getWriter、response已经被提交之后调用此方法，不会设置response
     * 的字符编码.
     * 
     * 如果协议支持，容器必须向客户端说明servlet response使用的字符编码和
     * 语言区域设置.如HTTP协议，可以通过响应头Content-Language来指定locale，
     * 通过Content-Type的一部分来指定字符编码.
     */
    public void setLocale(Locale loc);
    
    /**
     * 返回通过#setLocale指定的response语言区域.
     * 在response提交之后，调用#setLocale方法没有效果.
     * 如果没有指定语言区域，则返回容器默认的语言区域.
     */
    public Locale getLocale();
}
```