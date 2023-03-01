##### javax.servlet.ServletRequestAttributeEvent
```java
package javax.servlet;

    /** 
      * 应用中servlet request属性变化通知的事件类
      */
public class ServletRequestAttributeEvent extends ServletRequestEvent { 

    private static final long serialVersionUID = -1466635426192317793L;

    private String name;
    private Object value;

     /** 
      * 使用参数ServletContext、属性变化的ServletRequest、变化的属性name/value对象来构造
      * ServletRequestAttributeEvent实例
      */
    public ServletRequestAttributeEvent(ServletContext sc, ServletRequest request, String name, Object value) {
        super(sc, request);
        this.name = name;
        this.value = value;
    }

    /**
      * 返回ServletRequest变化的属性名
      */
    public String getName() {
        return this.name;
    }

    /**
      * 返回变化的属性值，对应关系如下:
      * 增加属性->增加的value
      * 删除属性->删除的value
      * 替换属性->被替换的value
      */
    public Object getValue() {
        return this.value;   
    }
}
```