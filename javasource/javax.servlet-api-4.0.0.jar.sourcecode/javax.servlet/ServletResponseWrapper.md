##### javax.servlet.ServletResponseWrapper
```java
package javax.servlet;

import java.io.IOException;
import java.io.PrintWriter;
import java.util.Locale;

/**
 * 为ServletResponse提供了一个便捷的实现，在对ServletResponse有改动需求的时，
 * 可以重写此类，此类类似ServletRequest，也实现了装饰器模式.
 * 方法的调用会通过包裹的response对象.
 */
public class ServletResponseWrapper implements ServletResponse {
    // ServletResponse实例
	private ServletResponse response;

	/**
    * constructor with response parameter
    * 
	* @throws java.lang.IllegalArgumentException if the response is null.
	*/
	public ServletResponseWrapper(ServletResponse response) {
	    if (response == null) {
    		throw new IllegalArgumentException("Response cannot be null");
	    }
	    this.response = response;
	}

	/**
	* 获取ServletResponse实例
	*/
	public ServletResponse getResponse() {
		return this.response;
	}	
	
	/**
	* 设置ServletResponse实例 
    * 
	* @throws java.lang.IllegalArgumentException if the response is null.
	*/
	public void setResponse(ServletResponse response) {
	    if (response == null) {
	    	throw new IllegalArgumentException("Response cannot be null");
	    }
	    this.response = response;
	}

    /**
     * 同ServletResponse#setCharacterEncoding
     */
    public void setCharacterEncoding(String charset) {
        this.response.setCharacterEncoding(charset);
    }

    /**
     * 同ServletResponse#getCharacterEncoding
     */
    public String getCharacterEncoding() {
	    return this.response.getCharacterEncoding();
	}
    
	/**
     * 同ServletResponse#getOutputStream
     */
    public ServletOutputStream getOutputStream() throws IOException {
	    return this.response.getOutputStream();
    }  
      
    /**
     * 同ServletResponse#getWriter
     */
    public PrintWriter getWriter() throws IOException {
	    return this.response.getWriter();
	}
    
    /**
     * 同ServletResponse#setContentLength
     */
    public void setContentLength(int len) {
	    this.response.setContentLength(len);
    }

    /**
     * 同ServletResponse#setContentLengthLong
     */
    public void setContentLengthLong(long len) {
        this.response.setContentLengthLong(len);
    }

    /**
     * 同ServletResponse#setContentType
     */
    public void setContentType(String type) {
	    this.response.setContentType(type);
    }

    /**
     * 同ServletResponse#getContentType
     */
    public String getContentType() {
	    return this.response.getContentType();
    }
    
    /**
     * 同ServletResponse#setBufferSize
     */
    public void setBufferSize(int size) {
	    this.response.setBufferSize(size);
    }
    
    /**
     * 同ServletResponse#getBufferSize
     */
    public int getBufferSize() {
	    return this.response.getBufferSize();
    }

    /**
     * 同ServletResponse#flushBuffer
     */
    public void flushBuffer() throws IOException {
	    this.response.flushBuffer();
    }
    
    /**
     * 同ServletResponse#isCommitted
     */
    public boolean isCommitted() {
	    return this.response.isCommitted();
    }

    /**
     * 同ServletResponse#reset
     */
    public void reset() {
	    this.response.reset();
    }
    
    /**
     * 同ServletResponse#resetBuffer
     */
    public void resetBuffer() {
	    this.response.resetBuffer();
    }
    
    /**
     * 同ServletResponse#setLocale
     */
    public void setLocale(Locale loc) {
	    this.response.setLocale(loc);
    }
    
    /**
     * 同ServletResponse#getLocale
     */
    public Locale getLocale() {
	    return this.response.getLocale();
    }

    /**
     * 递归检查是否ServletResponseWrapper包裹了给定的ServletResponse实例
     */
    public boolean isWrapperFor(ServletResponse wrapped) {
        if (response == wrapped) {
            return true;
        } else if (response instanceof ServletResponseWrapper) {
            return ((ServletResponseWrapper) response).isWrapperFor(wrapped);
        } else {
            return false;
        }
    }

    /**
     * 递归检查是否ServletResponseWrapper包裹了给定的类型的ServletResponse
     *
     * @throws IllegalArgumentException 如果参数wrappedType没有实现
     * ServletResponse接口
     */
    public boolean isWrapperFor(Class<?> wrappedType) {
        // 检查参数是否合法
        if (!ServletResponse.class.isAssignableFrom(wrappedType)) {
            throw new IllegalArgumentException("Given class " +
                wrappedType.getName() + " not a subinterface of " +
                ServletResponse.class.getName());
        }
        if (wrappedType.isAssignableFrom(response.getClass())) {
            return true;
        // 递归ServletResponseWrapper获取response对比
        } else if (response instanceof ServletResponseWrapper) {
            return ((ServletResponseWrapper) response).isWrapperFor(wrappedType);
        } else {
            return false;
        }
    }
}
```