##### javax.servlet.http.HttpServletResponseWrapper
```java
package javax.servlet.http;

import java.io.IOException;
import java.util.Collection;
import java.util.Map;
import java.util.function.Supplier;
import javax.servlet.ServletResponseWrapper;

/**
 * 提供了一个HttpServletResponse接口的便捷实现.
 */
public class HttpServletResponseWrapper extends ServletResponseWrapper implements HttpServletResponse {

    /** 
     * 构造器，用来创建包裹给定request的HttpServletResponseWrapper obj
     *
     * @throws java.lang.IllegalArgumentException request为null
     */
    public HttpServletResponseWrapper(HttpServletResponse response) {
        super(response);
    }
    
    /**
     * 获取包裹的HttpServletResponse对象.
     */
    private HttpServletResponse _getHttpServletResponse() {
        return (HttpServletResponse) super.getResponse();
    }

    /**
     * 同HttpServletResponse#addCookie(Cookie cookie)
     */
    @Override
    public void addCookie(Cookie cookie) {
        this._getHttpServletResponse().addCookie(cookie);
    }

    /**
     * 同HttpServletResponse#containsHeader(String name)
     */
    @Override
    public boolean containsHeader(String name) {
        return this._getHttpServletResponse().containsHeader(name);
    }

    /**
     * 同HttpServletResponse#encodeURL(String url)
     */
    @Override
    public String encodeURL(String url) {
        return this._getHttpServletResponse().encodeURL(url);
    }

    /**
     * 同HttpServletResponse#encodeRedirectURL(String url)
     */
    @Override
    public String encodeRedirectURL(String url) {
        return this._getHttpServletResponse().encodeRedirectURL(url);
    }

    /**
     * 同HttpServletResponse#encodeUrl(String url)
     */
    @Deprecated
    @Override
    public String encodeUrl(String url) {
        return this._getHttpServletResponse().encodeUrl(url);
    }

    /**
     * 同HttpServletResponse#encodeRedirectUrl(String url)
     */
    @Deprecated
    @Override
    public String encodeRedirectUrl(String url) {
        return this._getHttpServletResponse().encodeRedirectUrl(url);
    }

    /**
     * 同HttpServletResponse#sendError(int sc, String msg)
     */
    @Override
    public void sendError(int sc, String msg) throws IOException {
        this._getHttpServletResponse().sendError(sc, msg);
    }

    /**
     * 同HttpServletResponse#sendError(int sc)
     */
    @Override
    public void sendError(int sc) throws IOException {
        this._getHttpServletResponse().sendError(sc);
    }

    /**
     * 同HttpServletResponse#sendRedirect(String location)
     */
    @Override
    public void sendRedirect(String location) throws IOException {
        this._getHttpServletResponse().sendRedirect(location);
    }

    /**
     * 同HttpServletResponse#setDateHeader(String name, long date)
     */
    @Override
    public void setDateHeader(String name, long date) {
        this._getHttpServletResponse().setDateHeader(name, date);
    }

    /**
     * 同HttpServletResponse#addDateHeader(String name, long date)
     */
    @Override
    public void addDateHeader(String name, long date) {
        this._getHttpServletResponse().addDateHeader(name, date);
    }

    /**
     * 同HttpServletResponse#setHeader(String name, String value)
     */
    @Override
    public void setHeader(String name, String value) {
        this._getHttpServletResponse().setHeader(name, value);
    }

    /**
     * 同HttpServletResponse#addHeader(String name, String value)
     */
    @Override
    public void addHeader(String name, String value) {
        this._getHttpServletResponse().addHeader(name, value);
    }

    /**
     * 同HttpServletResponse#setIntHeader(String name, int value)
     */
    @Override
    public void setIntHeader(String name, int value) {
        this._getHttpServletResponse().setIntHeader(name, value);
    }

    /**
     * 同HttpServletResponse#addIntHeader(String name, int value)
     */
    @Override
    public void addIntHeader(String name, int value) {
        this._getHttpServletResponse().addIntHeader(name, value);
    }

    /**
     * 同HttpServletResponse#setStatus(int sc)
     */
    @Override
    public void setStatus(int sc) {
        this._getHttpServletResponse().setStatus(sc);
    }
    
    /**
     * 同HttpServletResponse#setStatus(int sc, String sm)
     */
    @Deprecated
    @Override
    public void setStatus(int sc, String sm) {
        this._getHttpServletResponse().setStatus(sc, sm);
    }

    /**
     * 同HttpServletResponse#getStatus
     */
    @Override
    public int getStatus() {
        return _getHttpServletResponse().getStatus();
    }

    /**
     * 同HttpServletResponse#getHeader(String name)
     */
    @Override
    public String getHeader(String name) {
        return _getHttpServletResponse().getHeader(name);
    }

    /**
     * 同HttpServletResponse#getHeaders(String name)
     */                      
    @Override
    public Collection<String> getHeaders(String name) {
        return _getHttpServletResponse().getHeaders(name);
    }

    /**
     * 同HttpServletResponse#getHeaderNames
     */
    @Override
    public Collection<String> getHeaderNames() {
        return _getHttpServletResponse().getHeaderNames();
    }

    /**
     * 同HttpServletResponse#setTrailerFields
     */
    @Override
    public void setTrailerFields(Supplier<Map<String, String>> supplier) {
        _getHttpServletResponse().setTrailerFields(supplier);
    }

    /**
     * 同HttpServletResponse#getTrailerFields
     */
    @Override
    public Supplier<Map<String, String>> getTrailerFields() {
        return _getHttpServletResponse().getTrailerFields();
    }
}
```