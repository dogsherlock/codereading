##### javax.servlet.ServletContextAttributeEvent
```java
package javax.servlet;

/** 
 * 此类代表在一个web应用中ServletContext属性变化的事件
 */
public class ServletContextAttributeEvent extends ServletContextEvent { 

    private static final long serialVersionUID = -5804680734245618303L;

    private String name;
    private Object value;

    /**
     * 给定ServletContext source对象、name/value对象来构造ServletContextAttributeEvent对象
     */
    public ServletContextAttributeEvent(ServletContext source,
            String name, Object value) {
        super(source);
        this.name = name;
        this.value = value;
    }
	
    /**
     * 返回ServletContext对象改变的属性名
     */
    public String getName() {
        return this.name;
    }
	
    /**
     * 返回ServletContext对象改变的属性名对应的属性值
     * 属性变化和value对应关系:
     * 添加->添加的value、移除->移除的value、替换->原来的value
     */
    public Object getValue() {
        return this.value;   
    }
}
```