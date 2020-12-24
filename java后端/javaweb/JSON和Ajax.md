# JSON

全称 JavaScript object notation ，是一种轻量级的数据交换格式。相比XML更轻量，用于客户端和服务端之间的数据交换，

## json 在JavaScript中使用

### json定义

json 是由键值对组成，并且有花括号包围。每个键由引号引起来，键和值之间使用冒号进行分隔，多组键值对之间进行逗号进行分隔。

```javascript
var json = {
				"key1":1,
				"key2":"abc",
				"key3":true,
				"key4":[1,"aa",false],
				"key5":{
					"key5_1":51,
					"key5_2":[2,"bb",true]
				}
				"key6":[{
					"key6_1_1":61,
					"key6_1_2":"bbb"
				},{
					"key6_2_1":33
				}]
			}
```

### json 访问

```javascript
alert(jsonObj);			//Object
alert(jsonObj.key1);	//1
alert(jsonObj.key4); 	//[1,"aa",false]
for (var i=0;i<jsonObj.key4.length; i++){
    alert(jsonObj.key4[i]);
}
alert(jsonObj.key6); 	//object,object
```

### json 对象和字符串

json有对象和字符串两种存在形式。

- json对象：主要用在操作json中数据的时候。
- json字符串：主要用在客户端和服务器之间进行数据交换。

两个常用方法：

- JSON.stringify()：将json对象转换成 json字符串
- JSON.parse()：将json字符串解析为 json对象

```javascript

// json对象转字符串
var jsonString = JSON.stringify(jsonObj);
alert(jsonString);	//key1:1,....
// json字符串转json对象
var jsonObj1 = JSON.parse(jsonString);
alert(jsonObj1);	//object Object
```



## json在java中使用

1. 导包：使用gson包

2. 方法：

   - toJson()：将javabean转换成 json字符串
   - fromJson() ：将json字符串 转换成 javabean

3. 实例

   1. javabean 与json的转换

   ```java
   //
   Person person = new Person(1, "张三");
   Gson gson = new Gson();
   String personJsonString = gson.toJson(person);
   System.out.println(personJsonString);
   Person personJavaBean = gson.fromJson(personJsonString, Person.class);
   System.out.println(personJavaBean);
   ```

   2. list  与 json的转换

      TypeToken 是Gson下的一个类，用来明确json字符串需要转换成的类型，此处使用匿名类然后获得它的类型。

   ```java
   List<Person> persons = new ArrayList<>();
   persons.add(new Person(1,"zhangsan"));
   persons.add(new Person(2,"lisi"));
   persons.add(new Person(3,"wangwu"));
   Gson gson = new Gson();
   String listToJson = gson.toJson(persons);
   System.out.println(listToJson); // json字符串
   Object jsonToList = gson.fromJson(listToJson, new TypeToken<ArrayList<Person>>() {}.getType());
   System.out.println(jsonToList); //list集合
   ```

   3. map 与 json的转换

   ```java
   Map<Integer, String> map = new HashMap<>();
           map.put(1,"aaa");
           map.put(2,"bbb");
           map.put(3,"ccc");
           Gson gson = new Gson();
           String mapToJson = gson.toJson(map);
           System.out.println(mapToJson);
           Object jsonToMap = gson.fromJson(mapToJson, new TypeToken<HashMap<Integer, String>>() {}.getType());
           System.out.println(jsonToMap);
   ```

   



# AJAX

全称：asynchronous javascript and xml。一种浏览器通过js异步发送请求，局部更新页面的技术。

Ajax请求实现局部更新，浏览器的地址不会发生变化，而且也不会舍弃原来页面的内容。

## JQuery中的 AJAX 请求

1.  $.ajax 方法

   ​		url:请求的地址

   ​		type: 请求的类型，GET或 POST

   ​		data:发送给服务器的数据。有两种： name=value&name=value  和 {key:value, key:value}

   ​		success: 请求成功后响应的回调函数

   ​		dataType:响应的数据类型。常用的数据类型：text纯文本，XML，json。

   ```javascript
   $.ajax({
      url: "..." 
      data: "action=createOrder",
      //data: {action:"createOrder"}
      type:"get",
      success:function(data){
       	alert(data.id);
   	},
      dataType:"json"
   });
   ```

   

2. $.get 和 $.post

   ​	url :

   ​	data:

   ​	callback:

   ​	type:

   ```java
   $.get("...","action=createOrder",function(data){..},"json");
   ```

   

3.  $.getJSON 

   ​	url :

   ​	data:

   ​	callback:

   ```java
   $.getJSON("...","action=createOrder",function(data){});
   ```

   















