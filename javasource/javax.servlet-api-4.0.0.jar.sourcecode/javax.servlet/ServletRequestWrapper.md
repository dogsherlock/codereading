##### javax.servlet.ServletRequestWrapper
```java
package javax.servlet;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.UnsupportedEncodingException;
import java.util.Enumeration;
import java.util.Locale;
import java.util.Map;

/**
 * 提供了ServletRequest接口的便捷实现，可以供开发者实现其子类来包装发往Servlet
 * 的请求.此类实现了装饰器模式，请求的方法默认会经过包装的request对象.
 */
public class ServletRequestWrapper implements ServletRequest {
    /**
     * ServletRequestWrapper持有ServletRequest的实例
     */
    private ServletRequest request;

    /**
     * 用给定的ServletRequest obj创建ServletRequestWrapper obj
     * @throws java.lang.IllegalArgumentException if the request is null
     */
    public ServletRequestWrapper(ServletRequest request) {
        if (request == null) {
            throw new IllegalArgumentException("Request cannot be null");   
        }
        this.request = request;
    }

    /**
     * 获取包装的request obj
     */
    public ServletRequest getRequest() {
        return this.request;
    }

    /**
     * 设置被包裹的request obj
     *
     * @throws java.lang.IllegalArgumentException if the request is null.
     */
    public void setRequest(ServletRequest request) {
        if (request == null) {
            throw new IllegalArgumentException("Request cannot be null");
        }
        this.request = request;
    }

    /**
     * 获取request属性
     */
    public Object getAttribute(String name) {
        return this.request.getAttribute(name);
    }

    /**
     * 获取request属性名集合
     */
    public Enumeration<String> getAttributeNames() {
        return this.request.getAttributeNames();
    }    

    /**
     * 获取request的字符编码格式
     */
    public String getCharacterEncoding() {
        return this.request.getCharacterEncoding();
    }

    /**
     * 设置request的字符编码格式
     */
    public void setCharacterEncoding(String enc)
            throws UnsupportedEncodingException {
        this.request.setCharacterEncoding(enc);
    }

    /**
     * 获取request的请求体长度
     */
    public int getContentLength() {
        return this.request.getContentLength();
    }

    /**
     * 同上
     */
    public long getContentLengthLong() {
        return this.request.getContentLengthLong();
    }

    /**
     * 获取request的MIME类型
     */
    public String getContentType() {
        return this.request.getContentType();
    }

    /**
     * 返回request的输入流
     */
    public ServletInputStream getInputStream() throws IOException {
        return this.request.getInputStream();
    }

    /**
     * 获取request的查询字符串或form表单数据
     */
    public String getParameter(String name) {
        return this.request.getParameter(name);
    }

    /**
     * 同ServletRequest#getParameterMap
     */
    public Map<String, String[]> getParameterMap() {
        return this.request.getParameterMap();
    }

    /**
     * 同ServletRequest#getParameterNames
     */
    public Enumeration<String> getParameterNames() {
        return this.request.getParameterNames();
    }

    /**
     * 同ServletRequest#getParameterValues
     */
    public String[] getParameterValues(String name) {
        return this.request.getParameterValues(name);
    }

    /**
     * 获取request的protocol
     */
    public String getProtocol() {
        return this.request.getProtocol();
    }

    /**
     * 获取request uri的scheme
     */
    public String getScheme() {
        return this.request.getScheme();
    }

    /**
     * 同ServletRequest#getServerName
     */
    public String getServerName() {
        return this.request.getServerName();
    }

    /**
     * 同ServletRequest#getServerPort
     */
    public int getServerPort() {
        return this.request.getServerPort();
    }

    /**
     * 同ServletRequest#getReader
     */
    public BufferedReader getReader() throws IOException {
        return this.request.getReader();
    }

    /**
     * 同ServletRequest#getRemoteAddr
     */
    public String getRemoteAddr() {
        return this.request.getRemoteAddr();
    }

    /**
     * 同ServletRequest#getRemoteHost
     */
    public String getRemoteHost() {
        return this.request.getRemoteHost();
    }

    /**
     * 给request设置属性
     */
    public void setAttribute(String name, Object o) {
        this.request.setAttribute(name, o);
    }

    /**
     * 从request移除属性
     */
    public void removeAttribute(String name) {
        this.request.removeAttribute(name);
    }

    /**
     * 同ServletRequest#getLocale
     */
    public Locale getLocale() {
        return this.request.getLocale();
    }

    /**
     * 同ServletRequest#getLocales
     */
    public Enumeration<Locale> getLocales() {
        return this.request.getLocales();
    }

    /**
     * 同ServletRequest#isSecure
     */
    public boolean isSecure() {
        return this.request.isSecure();
    }

    /**
     * 同ServletRequest#getRequestDispatcher
     */
    public RequestDispatcher getRequestDispatcher(String path) {
        return this.request.getRequestDispatcher(path);
    }

    /**
     * 同ServletRequest#getRealPath
     */
    @Deprecated
    public String getRealPath(String path) {
        return this.request.getRealPath(path);
    }
    
    /**
     * 同ServletRequest#getRemotePort
     */    
    public int getRemotePort(){
        return this.request.getRemotePort();
    }

    /**
     * 同ServletRequest#getLocalName
     */
    public String getLocalName(){
        return this.request.getLocalName();
    }

    /**
     * 同ServletRequest#getLocalAddr
     */       
    public String getLocalAddr(){
        return this.request.getLocalAddr();
    }

    /**
     * 同ServletRequest#getLocalPort
     */
    public int getLocalPort(){
        return this.request.getLocalPort();
    }

    /**
     * 同ServletRequest#getServletContext
     */
    public ServletContext getServletContext() {
        return request.getServletContext();
    }

    /**
     * 同ServletRequest#startAsync
     */
    public AsyncContext startAsync() throws IllegalStateException {
        return request.startAsync();
    }

    /**
     * 同ServletRequest#startAsync(ServletRequest, ServletResponse)
     */
    public AsyncContext startAsync(ServletRequest servletRequest,
                                   ServletResponse servletResponse)
            throws IllegalStateException {
        return request.startAsync(servletRequest, servletResponse);
    }

    /**
     * 同ServletRequest#isAsyncStarted
     */
    public boolean isAsyncStarted() {
        return request.isAsyncStarted();
    }

    /**
     * 同ServletRequest#isAsyncSupported
     */
    public boolean isAsyncSupported() {
        return request.isAsyncSupported();
    }

    /**
     * 同ServletRequest#getAsyncContext
     */
    public AsyncContext getAsyncContext() {
        return request.getAsyncContext();
    }

    /**
     * Checks (recursively) if this ServletRequestWrapper wraps the given
     * {@link ServletRequest} instance.
     *
     * @param wrapped the ServletRequest instance to search for
     *
     * @return true if this ServletRequestWrapper wraps the
     * given ServletRequest instance, false otherwise
     *
     * @since Servlet 3.0
     */
    public boolean isWrapperFor(ServletRequest wrapped) {
        if (request == wrapped) {
            return true;
        } else if (request instanceof ServletRequestWrapper) {
            return ((ServletRequestWrapper) request).isWrapperFor(wrapped);
        } else {
            return false;
        }
    }

    /**
     * 递归检查被包裹的ServletRequest对象是否为给定的类型
     *
     * @throws IllegalArgumentException 如果参数wrappedType
     * 没有实现ServletRequest接口
     */
    public boolean isWrapperFor(Class<?> wrappedType) {
       // 判断wrappedType是否合法(实现了此接口或者继承了此接口)
        if (!ServletRequest.class.isAssignableFrom(wrappedType)) {
            throw new IllegalArgumentException("Given class " +
                wrappedType.getName() + " not a subinterface of " +  
                ServletRequest.class.getName());
        }
        // 判断request是否是wrappedType的子类或者实现了wrappedType接口
        if (wrappedType.isAssignableFrom(request.getClass())) {
            return true;
        // 如果request是派生自ServletRequestWrapper的实例，则递归判断
        } else if (request instanceof ServletRequestWrapper) {
            return ((ServletRequestWrapper) request).isWrapperFor(wrappedType);
        } else {
            return false;
        }
    }

    /**
     * 同ServletRequest#getDispatcherType
     */
    public DispatcherType getDispatcherType() {
        return request.getDispatcherType();
    }
}
```