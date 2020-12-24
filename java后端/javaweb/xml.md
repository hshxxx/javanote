# XML

可扩展的标记语言，

作用：

1. 用来存储数据，这些数据有自我描述性。
2. 可以作为项目或模块的配置文件
3. 还可以作为网络传出数据的格式（现在用JSON）

## XML示例及注意事项

```xml
<?xml version="1.0" encoding="utf-8" ?>
<!-- 声明这是一个XML文件，版本是1.0，采用utf-8的编码格式-->

<books> <!--存储多个book的信息 -->
    <book>
        <id>1</id>
        <name>英语</name>
        <price>12</price>
    </book>
    <book>
        <id>2</id>
        <name>数学</name>
        <price><![CDATA[<<<<12>>>>]]></price>
        <!--CDATA 用来告诉系统这里的文本不用解析，直接打印即可 -->
    </book>
</books>
<person id="1" name="zhang"/>

```

注意：

1. XML的标签可以是双标签也可以是单标签。

2. XML标签中的属性是以名称值的形式定义。

3. XML标签必须是闭合的。

4. XML标签对大小写敏感

5. XML标签必须正确地嵌套

6. XML标签中没有父标签的元素叫做顶级元素，根元素是唯一的顶级元素。

7. CDATA语法： <![CDATA[<<123,..]]>

   用来告诉系统不要解析，直接打印即可

   最后打印 <<123,..

   先写一对尖括号，然后写一个感叹号，再写一对嵌套的方括号，在方括号之间写上CDATA，在最内层的方括号中间写上数据。

## XML文档的解析

首先，无论是HTML，还是XML，他们作为文档对象都可以解构成DOM树，所以在此基础上，有两种解析XML文档的方法。

最开始有DOM 和 Sax ，Dom解析技术由w3c组织指定，Sax由sun公司在DOM解析技术上升级。如今，这两种方式已经过时，主流的XML解析技术为第三方的 jdom，**dom4j**，pull这三种，其中jdom是对dom解析技术的封装，而dom4j 是对jdom的封装，pull解析技术用于Android开发。所以，我们应学习dom4j，同时dom4j的实现需要第三方提供的类库支持。

## dom4j 解析技术

```java
@Test
public void test1() throws DocumentException {
    SAXReader saxReader = new SAXReader();				//读取XML的sax
    Document document = saxReader.read("books.xml");
    Element rootElement = document.getRootElement();	//获取根元素，在此处就是books标签
    List<Element> books = rootElement.elements("book");	//获取多个book元素，将其存储到list中
    for (Element book : books) {
        String bookName = book.elementText("name");		//根据标签名获取标签内容
        String id = book.elementText("id");
        String price = book.elementText("price");
        String sn = book.attributeValue("sn");			//根据标签名获取属性名
        System.out.println(new Book(Integer.parseInt(sn),
                                    Integer.parseInt(id),bookName,
                                    new BigDecimal(price)));

    }

```

books.xml文件

```xml
<?xml version="1.0" encoding="utf-8" ?>
<books>
    <book sn="1">
        <id>1</id>
        <name>英语</name>
        <price>12.0</price>
    </book>
    <book sn="2">
        <id>2</id>
        <name>数学</name>
        <price>12.6</price>
    </book>
</books>
```

