##### javax.servlet.http.HttpServletMapping
```java
package javax.servlet.http;

/**
 * 用于URL映射的运行时发现
 * 任何方法的调用者都不能阻塞调用者，实现必须是线程安全的,实例不可变并且由
 * eg: 
 * <table border="1">
 *   <caption>Expected values of properties for various URI paths</caption>
 *   <tr>
 *     <th>URI Path (in quotes)</th>
 *     <th>matchValue</th>
 *     <th>pattern</th>
 *     <th>mappingMatch</th>
 *   </tr>
 *   <tr>
 *     <td>""</td>
 *     <td>""</td>
 *     <td>""</td>
 *     <td>CONTEXT_ROOT</td>
 *   </tr>
 *   <tr>
 *     <td>"/index.html"</td>
 *     <td>""</td>
 *     <td>/</td>
 *     <td>DEFAULT</td>
 *   </tr>
 *   <tr>
 *     <td>"/MyServlet"</td>
 *     <td>MyServlet</td>
 *     <td>/MyServlet</td>
 *     <td>EXACT</td>
 *   </tr>
 *   <tr>
 *     <td>"/foo.extension"</td>
 *     <td>foo</td>
 *     <td>*.extension</td>
 *     <td>EXTENSION</td>
 *   </tr>
 *   <tr>
 *     <td>"/path/foo"</td>
 *     <td>foo</td>
 *     <td>/path/*</td>
 *     <td>PATH</td>
 *   </tr>  
 * </table>
 */
public interface HttpServletMapping {
    /**
     * 返回匹配的类型,如果getMappingMatch返回的匹配的类型是CONTEXT_ROOT或者
     * DEFAULT,这个方法必须返回空字符串, 如果getMappingMatch返回的是EXACT,
     * 这个方法必须返回匹配的路径(省略掉开头的/)
     * 如果getMappingMatch返回的是EXTENSION或PATH,那么这个方法必须返回匹配*的值
     */
    public String getMatchValue();

    /**
     * 返回激活servlet请求的URL模式, 如果getMappingMatch的值是EXTENSION,
     * 此方法必须返回没有/的模式
     */
    public String getPattern();
    
    /**
     * 返回被请求激活的servlet类的全限定名
     */
    public String getServletName();

    /**
     * 返回当前实例的匹配类型
     */
    public MappingMatch getMappingMatch();
}
```