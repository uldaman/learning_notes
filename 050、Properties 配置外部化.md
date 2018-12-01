转自: [Java 中 Properties 类的使用](http://www.2cto.com/px/201006/47834.html)

Java 中的 Properties 文件是一种配置文件, 主要用于表达配置信息, 文件类型为 `*.properties`, 格式为文本文件, 文件的内容是格式是 "**键=值**" 的格式, 在 Properties 文件中, 可以用 "**#**" 来作注释, Properties 文件在 Java 编程中用到的地方很多, 操作很方便.

# Properties 文件
一个 Properties 文件的例子: `test.properties`

```ini
#################################
#   工商报表应用 IcisReport 的配置文件 #
#   日期: 2006年11月21日 #
#################################
#
#   说明:业务系统 TopIcis 和报表系统 IcisReport 是分离的
#   可分开部署到不同的服务器上, 也可以部署到同一个服务
#   器上; IcisReprot 作为独立的 web 应用程序可以使用任何
#   的 Servlet 容器或者 J2EE 服务器部署并单独运行, 也可以
#   通过业务系统的接口调用作为业务系统的一个库来应用.
#
#   IcisReport 的 ip
IcisReport.server.ip=192.168.3.143
#   IcisReport 的端口
IcisReport.server.port=8080
#   IcisReport 的上下文路径
IcisReport.contextPath=/IcisReport
```

# Properties 类
Java 用 `Properties` 类来操作 Properties 文件, `Properties` 类存在于包 `Java.util` 中, 该类继承自 `Hashtable`.

- `getProperty(String  key)`, 用指定的键在此属性列表中搜索属性. 也就是通过参数 key , 得到 key 所对应的 value
- `load(InputStream inStream)`, 从输入流中读取属性列表 (键和元素对) . 通过对指定的文件 (比如说上面的 `test.properties` 文件) 进行装载来获取该文件中的所有 **键\-值** 对. 以供 `getProperty(String  key)` 来搜索
- `setProperty(String key, String value)`, 调用 `Hashtable` 的方法 `put`. 他通过调用基类的 `put` 方法来设置 **键\-值** 对.
- `store(OutputStream out, String comments)`, 以适合使用 `load` 方法加载到 `Properties` 表中的格式, 将此 `Properties` 表中的属性列表 (键和元素对) 写入输出流. 与 `load` 方法相反, 该方法将 **键\-值** 对写入到指定的文件中去
- `propertyNames()`, 得到配置文件中 key 的 `Enumeration`, 通过 `Enumeration` 的 `hasMoreElements()` 及 `nextElement()` 方法就能遍历整个 Properties
- `clear()`, 清除所有装载的 **键\-值** 对. 该方法在基类中提供

# 读取 Properties 文件
Java读取 Properties 文件的方法有很多.

但是最常用的还是通过 `java.lang.Class` 类的 `getResourceAsStream(String name)` 方法来实现, 如下可以这样调用:

`InputStream in = getClass().getResourceAsStream("资源Name");`

作为我们写程序的, 用此一种足够; 或者下面这种也常用:

`InputStream in = new BufferedInputStream(new FileInputStream(filepath));`

# 实例
```java
 // 关于 Properties 类常用的操作
 public class TestProperties {
     // 根据 Key 读取 Value
     public static String GetValueByKey(String filePath, String key) {
         Properties pps = new Properties();
         try {
             InputStream in = new BufferedInputStream (new FileInputStream(filePath));
             pps.load(in);
             String value = pps.getProperty(key);
             System.out.println(key + " = " + value);
             return value;

         }catch (IOException e) {
             e.printStackTrace();
             return null;
         }
     }

     // 读取 Properties 的全部信息
     public static void GetAllProperties(String filePath) throws IOException {
         Properties pps = new Properties();
         InputStream in = new BufferedInputStream(new FileInputStream(filePath));
         pps.load(in);
         Enumeration en = pps.propertyNames(); // 得到配置文件的名字

         while(en.hasMoreElements()) {
             String strKey = (String) en.nextElement();
             String strValue = pps.getProperty(strKey);
             System.out.println(strKey + "=" + strValue);
         }

     }

     // 写入 Properties 信息
     public static void WriteProperties (String filePath, String pKey, String pValue) throws IOException {
         Properties pps = new Properties();

         InputStream in = new FileInputStream(filePath);
         // 从输入流中读取属性列表（键和元素对）
         pps.load(in);
         // 调用 Hashtable 的方法 put, 使用 getProperty 方法提供并行性.
         // 强制要求为属性的键和值使用字符串. 返回值是 Hashtable 调用 put 的结果.
         OutputStream out = new FileOutputStream(filePath);
         pps.setProperty(pKey, pValue);
         // 以适合使用 load 方法加载到 Properties 表中的格式，
         // 将此 Properties 表中的属性列表（键和元素对）写入输出流
         pps.store(out, "Update " + pKey + " name");
     }

     public static void main(String [] args) throws IOException{
         // String value = GetValueByKey("Test.properties", "name");
         // System.out.println(value);
         // GetAllProperties("Test.properties");
         WriteProperties("Test.properties","long", "212");
     }
 }
```

