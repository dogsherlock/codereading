##### javax.servlet.http.HttpServletRequestWrapper
```java
package javax.servlet.http;

import java.io.IOException;
import java.util.*;
import javax.servlet.ServletException;
import javax.servlet.ServletRequestWrapper;

/**
 * 提供HttpServletRequest接口的便捷实现，在对HttpServletRequest有改动
 * 需求的时候，可以重写此类.
 *
 * 此类实现了装饰器模式，调用的方法默认都会通过被包裹的request obj.
 */
public class HttpServletRequestWrapper extends ServletRequestWrapper implements HttpServletRequest {

    /** 
     * 构造器，用来创建包裹给定request的HttpServletRequestWrapper obj.
     *
     * @throws java.lang.IllegalArgumentException request为null
     */
    public HttpServletRequestWrapper(HttpServletRequest request) {
        super(request);
    }
    
    /**
     * 获取包裹的HttpServletRequest对象.
     */
    private HttpServletRequest _getHttpServletRequest() {
        return (HttpServletRequest) super.getRequest();
    }

    /**
     * 同HttpServletRequest#getAuthType
     */
    @Override
    public String getAuthType() {
        return this._getHttpServletRequest().getAuthType();
    }
   
    /**
     * 同HttpServletRequest#getCookies
     */
    @Override
    public Cookie[] getCookies() {
        return this._getHttpServletRequest().getCookies();
    }

    /**
     * 同HttpServletRequest#getDateHeader
     */
    @Override
    public long getDateHeader(String name) {
        return this._getHttpServletRequest().getDateHeader(name);
    }
               
    /**
     * 同HttpServletRequest#getHeader
     */
    @Override
    public String getHeader(String name) {
        return this._getHttpServletRequest().getHeader(name);
    }
    
    /**
     * 同HttpServletRequest#getHeaders
     */
    @Override
    public Enumeration<String> getHeaders(String name) {
        return this._getHttpServletRequest().getHeaders(name);
    }  

    /**
     * 同HttpServletRequest#getHeaderNames
     */
    @Override
    public Enumeration<String> getHeaderNames() {
        return this._getHttpServletRequest().getHeaderNames();
    }
    
    /**
     * 同HttpServletRequest#getIntHeader
     */
    @Override
    public int getIntHeader(String name) {
        return this._getHttpServletRequest().getIntHeader(name);
    }

    /**
     * 同HttpServletRequest#getHttpServletMapping
     */
    @Override
    public HttpServletMapping getHttpServletMapping() {
        return this._getHttpServletRequest().getHttpServletMapping();
    }

    /**
     * 同HttpServletRequest#getMethod
     */
    @Override
    public String getMethod() {
        return this._getHttpServletRequest().getMethod();
    }
    
    /**
     * 同HttpServletRequest#getPathInfo
     */
    @Override
    public String getPathInfo() {
        return this._getHttpServletRequest().getPathInfo();
    }

    /**
     * 同HttpServletRequest#getPathTranslated
     */
    @Override
    public String getPathTranslated() {
        return this._getHttpServletRequest().getPathTranslated();
    }

    /**
     * 同HttpServletRequest#getContextPath
     */
    @Override
    public String getContextPath() {
        return this._getHttpServletRequest().getContextPath();
    }
    
    /**
     * 同HttpServletRequest#getQueryString
     */
    @Override
    public String getQueryString() {
        return this._getHttpServletRequest().getQueryString();
    }
    
    /**
     * 同HttpServletRequest#getRemoteUser
     */
    @Override
    public String getRemoteUser() {
        return this._getHttpServletRequest().getRemoteUser();
    }
    
    /**
     * 同HttpServletRequest#isUserInRole(String role)
     */
    @Override
    public boolean isUserInRole(String role) {
        return this._getHttpServletRequest().isUserInRole(role);
    }
    
    /**
     * 同HttpServletRequest#getUserPrincipal
     */
    @Override
    public java.security.Principal getUserPrincipal() {
        return this._getHttpServletRequest().getUserPrincipal();
    }
    
    /**
     * 同HttpServletRequest#getRequestedSessionId
     */
    @Override
    public String getRequestedSessionId() {
        return this._getHttpServletRequest().getRequestedSessionId();
    }
    
    /**
     * 同HttpServletRequest#getRequestURI
     */
    @Override
    public String getRequestURI() {
        return this._getHttpServletRequest().getRequestURI();
    }

    /**
     * 同HttpServletRequest#getRequestURL
     */
    @Override
    public StringBuffer getRequestURL() {
        return this._getHttpServletRequest().getRequestURL();
    }
        
    /**
     * 同HttpServletRequest#getServletPath
     */
    @Override
    public String getServletPath() {
        return this._getHttpServletRequest().getServletPath();
    }
    
    /**
     * 同HttpServletRequest#getSession(boolean create)
     */
    @Override
    public HttpSession getSession(boolean create) {
        return this._getHttpServletRequest().getSession(create);
    }
    
    /**
     * 同HttpServletRequest#getSession
     */
    @Override
    public HttpSession getSession() {
        return this._getHttpServletRequest().getSession();
    }
    
    /**
     * 同HttpServletRequest#changeSessionId
     */
    @Override
    public String changeSessionId() {
        return this._getHttpServletRequest().changeSessionId();
    }
    
    /**
     * 同HttpServletRequest#isRequestedSessionIdValid
     */
    @Override
    public boolean isRequestedSessionIdValid() {
        return this._getHttpServletRequest().isRequestedSessionIdValid();
    }
     
    /**
     * 同HttpServletRequest#isRequestedSessionIdFromCookie
     */
    @Override
    public boolean isRequestedSessionIdFromCookie() {
        return this._getHttpServletRequest().isRequestedSessionIdFromCookie();
    }
    
    /**
     * 同HttpServletRequest#isRequestedSessionIdFromURL
     */
    @Override
    public boolean isRequestedSessionIdFromURL() {
        return this._getHttpServletRequest().isRequestedSessionIdFromURL();
    }

    /**
     * 同HttpServletRequest#isRequestedSessionIdFromUrl
     */
    @Deprecated
    @Override
    public boolean isRequestedSessionIdFromUrl() {
        return this._getHttpServletRequest().isRequestedSessionIdFromUrl();
    }

    /**
     * 同HttpServletRequest#authenticate
     */
    @Override
    public boolean authenticate(HttpServletResponse response)
            throws IOException, ServletException {
        return this._getHttpServletRequest().authenticate(response);
    }

    /**
     * 同HttpServletRequest#login
     */
    @Override
    public void login(String username, String password)
            throws ServletException {
        this._getHttpServletRequest().login(username,password);
    }

    /**
     * 同HttpServletRequest#logout
     */
    @Override
    public void logout() throws ServletException {
        this._getHttpServletRequest().logout();
    }

    /**
     * 同HttpServletRequest#getParts
     */
    @Override
    public Collection<Part> getParts() throws IOException, ServletException {
        return this._getHttpServletRequest().getParts(); 
    }

    /**
     * 同HttpServletRequest#getPart(String name)
     */
    @Override
    public Part getPart(String name) throws IOException, ServletException {
        return this._getHttpServletRequest().getPart(name); 
    
    }

    /**
     * 同HttpServletRequest#upgrade
     */
    @Override
    public <T extends HttpUpgradeHandler> T upgrade(Class<T> handlerClass)
            throws IOException, ServletException {
        return this._getHttpServletRequest().upgrade(handlerClass);
    }

    /**
     * 同HttpServletRequest#newPushBuilder
     */
    @Override
    public PushBuilder newPushBuilder() {
        return this._getHttpServletRequest().newPushBuilder();
    }

    /**
     * 同HttpServletRequest#getTrailerFields
     */
    @Override
    public Map<String, String> getTrailerFields() {
        return this._getHttpServletRequest().getTrailerFields();
    }

    /**
     * 同HttpServletRequest#isTrailerFieldsReady
     */
    @Override
    public boolean isTrailerFieldsReady() {
        return this._getHttpServletRequest().isTrailerFieldsReady();
    }
}
```