##### javax.servlet.Registration
```java
package javax.servlet;

import java.util.Map;
import java.util.Set;

/**
 * 通过Registration接口，servlet和filter可以进一步配置.
 *
 * Registration obj初始化情况下#getClassName方法返回null.由特定容器实现的servlet
 * 和filter类可能没有声明<servlet-class>或<filter-class>标签，并且会被表示为
 * 初步的Registration obj. 初步注册必须调用在ServletContext obj上的
 * #addServlet或#addFilter方法来完成.并且要通过#getName传入servlet或filter的名字以及
 * 支持servlet或filter实现类的name和类对象，或者实例.在大多数情况下，初步注册将会通过容器
 * 提供的实现ServletContainerInitializer接口的类来完成.
 */
public interface Registration {

    /**
     * 获取由Registration obj代表的servlet或filter的name.
     */
    public String getName();

    /**
     * 获取由Registration obj代表的servlet或filter的全限定类名.
     */
    public String getClassName();

    /**
     * 设置由Registration obj代表的servlet或filter的初始参数name/value.
     *
     * @return 如果设置成功，返回true.也就是初始参数的name在由Registration obj代表的
     * servlet或filter中不存在，否则返回false.
     *
     * @throws IllegalStateException 如果ServletContext已经被初始化.
     *
     * @throws IllegalArgumentException name或value为null.
     */ 
    public boolean setInitParameter(String name, String value);

    /**
     * 使用给定的name获取初始化参数value.
     */ 
    public String getInitParameter(String name);

    /**
     * 批量设置初始化参数.
     *
     * 对于map中每个初始化参数都会调用#setInitParameter(String,String).
     * 如果方法返回false，则不会更新.如果初始化参数的name或value是null，
     * 则抛出IllegalArgumentException异常.
     *
     * @param initParameters 初始化参数
     *
     * @return 初始化参数name冲突的集合(可能为空) 
     *
     * @throws IllegalStateException 如果ServletContext已经被初始化.
     *
     * @throws IllegalArgumentException 如果参数map包含的初始化参数name或value为null.
     */ 
    public Set<String> setInitParameters(Map<String, String> initParameters);

    /**
     * 返回Registration obj中包含当前可用的初始化参数的不可变map(可能为空).
     */ 
    public Map<String, String> getInitParameters();

    /**
     * 通过接口servlet或filter分别可以通过addServlet方法或addFilter方法注册，在
     * ServletContext进一步配置.
     */
    interface Dynamic extends Registration {

        /**
         * 配置表示servlet或filter的动态Registration obj是否支持异步操作.
         * 默认不支持.调用此方法会覆盖从前的设置.
         *
         * @throws IllegalStateException 如果ServletContext已经被初始化.
         */
        public void setAsyncSupported(boolean isAsyncSupported);
    }
}
```