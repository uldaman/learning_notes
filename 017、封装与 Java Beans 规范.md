JavaBeans 是 Java 中一种特殊的类, 可以将多个对象封装到一个对象（bean）中, 特点是**可序列化**, 提供**无参构造器**, 提供 **getter** 方法和 **setter** 方法访问对象的属性.

**Java Beans 规范可以说是体现面向对象封装思想的一种体现.**

要成为 JavaBean 类, 则必须遵循关于命名、构造器、方法的特定规范; 有了这些规范, 才能有可以使用、复用、替代和连接 JavaBeans 的工具.

**规范如下**:

- 有一个公有的无参构造器.
- 属性可以通过get、set、is（可以替代get，用在布尔型属性上）方法或遵循特定命名规范的其他方法访问.
- 可序列化.

我们编写类的时候, 最好就是按 Java Beans 的规范来定义, 特别是 getter、setter 方法可以很好的控制业务逻辑.

```java
public class PersonBean {

    /**
     * name 属性(注意大小写)
     */
    private String name = null;

    private boolean deceased = false;

    /** 无参构造器(没有参数) */
    public PersonBean() {
    }

    /**
     * name 属性的Getter方法
     */
    public String getName() {
        return name;
    }

    /**
     * name 属性的Setter方法
     * @param value
     */
    public void setName(final String value) {
        name = value;
    }

    /**
     * deceased 属性的Getter方法
     * 布尔型属性的Getter方法的不同形式(这里使用了is而非get)
     */
    public boolean isDeceased() {
        return deceased;
    }

    /**
     * deceased 属性的Setter方法
     * @param value
     */
    public void setDeceased(final boolean value) {
        deceased = value;
    }
}
```
