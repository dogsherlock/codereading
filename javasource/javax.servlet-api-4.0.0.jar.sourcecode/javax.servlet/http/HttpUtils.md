##### javax.servlet.http.HttpUtils
```java
package javax.servlet.http;

import javax.servlet.ServletInputStream;
import java.util.Hashtable;
import java.util.ResourceBundle;
import java.util.StringTokenizer;
import java.io.IOException;

/**
 * @deprecated As of Java(tm) Servlet API 2.3.
 * 
 * 这些方法对于默认编码有用，被移到了请求相关接口.
 */
@Deprecated
public class HttpUtils {
    /**
    * base name of resources bundle
    */
    private static final String LSTRING_FILE = 
    "javax.servlet.http.LocalStrings";

    /**
     * 错误信息国际化
     */
    private static ResourceBundle lStrings =
	ResourceBundle.getBundle(LSTRING_FILE);
        
    /**
     * 无参构造器.
     */
    public HttpUtils() {}

    /**
     * 解析客户端发给服务器的查询字符串，构建一个拥有key-value对的HashTable对象.
     *
     * 在查询字符串中，一个key可能有多个值，使用HashTable存储key-values.
     * 
     * 在hashtable中key和value是以解码形式存储，所以任何+字符被转换成空格，
     * 以十六进制发送的字符会被转换成对应的ASCII字符.
     * 如(%32->'2'， %2f->'/').
     *
     * @exception IllegalArgumentException 查询字符串是无效的
     */
    public static Hashtable<String, String[]> parseQueryString(String s) {

        String valArray[] = null;
	
        if (s == null) {
            throw new IllegalArgumentException();
        }

        Hashtable<String, String[]> ht = new Hashtable<String, String[]>();
        StringBuilder sb = new StringBuilder();
        // 将查询字符串按照&进行切分成tokens.
        StringTokenizer st = new StringTokenizer(s, "&");
        while (st.hasMoreTokens()) {
            String pair = st.nextToken();
            int pos = pair.indexOf('=');
            // 不符合key=value格式
            if (pos == -1) {
                // XXX
                // should give more detail about the illegal argument
                throw new IllegalArgumentException();
            }
            // 使用parseName对name和value进行解析.
            String key = parseName(pair.substring(0, pos), sb);
            String val = parseName(pair.substring(pos+1, pair.length()), sb);
            if (ht.containsKey(key)) {
                String oldVals[] = ht.get(key);
                valArray = new String[oldVals.length + 1];
                for (int i = 0; i < oldVals.length; i++) {
                    valArray[i] = oldVals[i];
                }
                valArray[oldVals.length] = val;
            } else {
                valArray = new String[1];
                valArray[0] = val;
            }
            ht.put(key, valArray);
        }
	    return ht;
    }

    /**
     * 解析客户端使用HTTP POST方法和MIME类型为application/x-www-form-urlencoded
     * 发给服务器的表单数据.
     *
     * @param len ServletInputStream发送的字节数
     *
     * @exception IllegalArgumentException POST方法发送的数据无效.
     */
    public static Hashtable<String, String[]> parsePostData(int len, 
                ServletInputStream in) {
        if (len <=0) {
            // cheap hack to return an empty hash
            return new Hashtable<String, String[]>(); 
        }

        if (in == null) {
            throw new IllegalArgumentException();
        }
	
        //
        // 确保读取完整的POST请求体内容.
        //
        byte[] postedBytes = new byte [len];
        try {
            int offset = 0;
            do {
                int inputLen = in.read(postedBytes, offset, len - offset);
                // len此处不为0，则至少读取一个字节或者返回-1.inputLen不可能为0.
                if (inputLen <= 0) {
                    String msg = lStrings.getString("err.io.short_read");
                    // msg = "Short Read";
                    throw new IllegalArgumentException (msg);
                }
                offset += inputLen;
            } while ((len - offset) > 0);
        } catch (IOException e) {
            throw new IllegalArgumentException(e.getMessage());
        }

        // XXX 我们不能认定POST请求体只可能是用ASCII/ISO Latin/1编码
        // 的表单数据
        try {
            // 使用iso8859-1解码字节数据.
            String postedBody = new String(postedBytes, 0, len, "8859_1");
            return parseQueryString(postedBody);
        } catch (java.io.UnsupportedEncodingException e) {
            // XXX 不支持此编码格式，此方法也许可以接受一个指定编码格式的参数.
            throw new IllegalArgumentException(e.getMessage());
        }
    }

    /*
     * 解析查询字符串中的name.
     */
    private static String parseName(String s, StringBuilder sb) {
        sb.setLength(0);
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i); 
            switch (c) {
                // '+' -> ' '
                case '+':
                    sb.append(' ');
                    break;
                // '%xx' -> 'the associated ascii value.' 
                case '%':
                    try {
                        sb.append((char) Integer.parseInt(s.substring(i+1, i+3), 
                                16));
                        i += 2;
                    } catch (NumberFormatException e) {
                        // XXX
                        // need to be more specific about illegal arg
                        throw new IllegalArgumentException();
                    } catch (StringIndexOutOfBoundsException e) {
                        String rest  = s.substring(i);
                        sb.append(rest);
                        if (rest.length()==2)
                            i++;
                        }

                        break;
                default:
                    sb.append(c);
                    break;
            }
        }

        return sb.toString();
    }

    /**
     * 利用HttpServletRequest对象中的信息返回客户端发送请求的URL.
     * 返回的URL如scheme://host[:port]/path，不包含查询字符串参数.
     * 
     * 此方法返回StringBuffer对象，你可以轻松改变URL，如添加查询字符串.
     * 
     * 见HttpServletRequest#getRequestURL
     */
    public static StringBuffer getRequestURL (HttpServletRequest req) {
        StringBuffer url = new StringBuffer();
        String scheme = req.getScheme ();
        int port = req.getServerPort ();
        String urlPath = req.getRequestURI();

        //String		servletPath = req.getServletPath ();
        //String		pathInfo = req.getPathInfo ();
        
        url.append (scheme);		// http, https
        url.append ("://");
        url.append (req.getServerName ());
        // 如果使用的协议默认端口，则不添加，否则，添加.
        if ((scheme.equals ("http") && port != 80)
        || (scheme.equals ("https") && port != 443)) {
            url.append (':');
            // url.append(port);
            url.append (req.getServerPort());
        }
        //if (servletPath != null)
        //    url.append (servletPath);
        //if (pathInfo != null)
        //    url.append (pathInfo);
        url.append(urlPath);

        return url;
    }
}
```