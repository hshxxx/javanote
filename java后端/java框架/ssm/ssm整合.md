# 创建Maven工程并配置和测试

1. idea中创建一个Maven项目，在src/main目录下手动创建webapp/WEB_INF/web.xml文件。

2. 在artifacts下添加新建的Maven项目。

3. 在run选项下将新建的Maven项目配置tomcat服务器。

4. 在本地的Maven的conf目录下的setting.xml文件中设置jdk版本和阿里云镜像资源库。

5. 在idea的pom.xml中配置spring、springmvc、mybatis、数据库连接池、驱动包。

6. 引入bootstrap前端框架。

7. 在src/resources目录下新建applicationContext.xml和dispatchServlet.xml。在web.xml 中配置bootstrap，spring，springmvc，字符编码，resturl。

8. 配置dispatchServlet.xml：注解扫描，视图解析器，两个标准配置。

9. 配置applicationContext.xml：数据源、mybatis、事务

10. 配置mybatis-generator：pom.xml中配置，然后新建一个测试类

11. 配置spring单元测试：pom.xml中配置，然后使用注解标注测试类

12. 修改mbg自动生成的mapper文件：修改EmployeeMapper接口和对应的xml文件，增加了以下两个方法

    ```
    List<Employee> selectByExampleWithDept(EmployeeExample example);
    Employee selectByPrimaryKeyWithDept(Integer id);
    ```

13. 测试Mapper：单个添加，批量操作。

    批量操作：在applicationContext中声明一个sqlSession用			来批量操作。

# 数据库表设计

不同表之间字段名最好不要一样，否则在联结查询时会出现字段冲突，MySQL会自动修改重复的字段名，导致服务端获取不到正确的数据。

建议表内字段名使用  **表名首字母小写_字段名**，如 employee表中主键为 e_id

对于表内的外键使用   外键所属表名_外键的主键，如employee表中外键为 dept_id



# 分页和springMVC测试

借助pagehelper插件完成。

1. Maven中添加依赖

2. mybatis_config.xml 中配置插件

   ```xml
   <plugins>
       <!--pagehelper插件 -->
       <!-- com.github.pagehelper为PageHelper类所在包名 -->
       <plugin interceptor="com.github.pagehelper.PageInterceptor">
       </plugin>
   </plugins>
   ```


## 使用web请求实现分页

编写Employee的controller层和service层

```java
@Controller
public class EmployeeController {
    @Autowired
    private EmployeeService employeeService;

    @RequestMapping("/emps")
     public String getEmps(
            @RequestParam(value = "pageNo" ,defaultValue = "1") Integer pageNo,
            Model model){
        //1.直接设置PageHelper的属性：当前页码和当前页的尺寸
        PageHelper.startPage(pageNo,5);
        //2.从数据库中查找所有Employee
        List<Employee> allEmps = employeeService.getAllEmps();
        //3.将存储所有Employee的集合放在PageInfo对象中，并设置每页可以显示的页数
        PageInfo<Employee> employeePageInfo = new PageInfo<Employee>(allEmps, 5);
        //4.将PageInfo对象存入到model
        model.addAttribute("pageInfo",employeePageInfo);
        return "list";
    }
}
=============
@Service
public class EmployeeService {
    @Autowired
    private EmployeeMapper employeeMapper;

    public List<Employee> getAllEmps(){
        //不传参就是查找所有
        return employeeMapper.selectByExampleWithDept(null);
    }
}    
```

## 使用ajax+json实现分页

编写Employee的controller层和service层

```java
@Controller
public class EmployeeController {
    @Autowired
    private EmployeeService employeeService;

    //使用ajax+json实现查询
    @ResponseBody
    @RequestMapping("/emps")
    public Msg getEmpsWithJson(@RequestParam(value = "pageNo" ,defaultValue = "1") Integer pageNo){
        PageHelper.startPage(pageNo,5);
        List<Employee> allEmps = employeeService.getAllEmps();
        PageInfo<Employee> employeePageInfo = new PageInfo<Employee>(allEmps, 5);
        Msg msg = Msg.success();
        msg.add("pageInfo",employeePageInfo);
        return msg;
    }
}
=============
@Service
public class EmployeeService {
    @Autowired
    private EmployeeMapper employeeMapper;

    public List<Employee> getAllEmps(){
        //不传参就是查找所有
        return employeeMapper.selectByExampleWithDept(null);
    }
}    
```

## springmvc测试

1. 编写springMVC测试类

   ```java
   @RunWith(SpringJUnit4ClassRunner.class)
   @WebAppConfiguration
   @ContextConfiguration(locations = {"classpath:applicationContext.xml","classpath:dispatchServlet.xml"})
   public class MvcTest {
       @Autowired
       WebApplicationContext context;
   
       MockMvc mockMvc;
       @Before
       public void initMockMvc(){
           mockMvc = MockMvcBuilders.webAppContextSetup(context).build();
       }
       @Test
       public void testPageNo() throws Exception {
           MvcResult result = mockMvc.perform(MockMvcRequestBuilders.get("/emps").param("pageNo", "1")).andReturn();
           MockHttpServletRequest request = result.getRequest();
           PageInfo pageInfo = (PageInfo)request.getAttribute("pageInfo");
           System.out.println("当前页："+pageInfo.getPageNum());
           System.out.println("总页码："+pageInfo.getPages());
           System.out.println("总记录数："+pageInfo.getTotal());
           System.out.print("每页需要显示的页码：");
           int[] nums = pageInfo.getNavigatepageNums();
           for(int i : nums){
               System.out.print(""+i);
           }
           List<Employee> list = pageInfo.getList();
           for(Employee e : list){
               System.out.println("ID："+e.getId()+"====name:"+e.getName());
           }
       }
   }
   ```


# ajax 注意事项

```javascript
$.ajax({
    url:"/ssm_integration/empname",
    data:"emp_name="+emp_name,
    type:"get",
    success:function(result) {
        var code = result.code;
        if(code == 200){
            modifiy_label("#emp_name","has-error","用户名不可用");
        }
    }
});
```



1. 接受ajax请求的服务端方法需要用 **@ResponseBody**标注后才能正确返回数据
2. data：**"emp_name="+emp_name** 写成键值对形式后端才能根据参数名获取参数值。

## PUT 类型的AJAX

1. web.xml中 配置 HttpPutFormContentFilter。
2. 原理：将请求体中的数据包装成map，并将原来的request和这个map包装成一个新的request。服务端在获取参数时，要么从原有request中拿，要么从包装之后的request中拿。

# 数据校验

前后端校验共同组成数据校验

1. 前端校验
   1. 用户名改变时要校验
   2. 提交表单时要检验
2. 后端校验
   1. JSR303校验
   2. 数据库加唯一约束

# RESTUrl 风格

- /emp/{id} ：  GET 查询员工
- /emp：        POST 保存员工
- /emp/{id}：PUT 修改员工
- /emp/{id}：DELETE 删除员工

1. springMVC 会自动封装 url 带的参数给服务端方法的参数

   ```java
   //前端
   $.ajax({
     url:"/ssm_integration/emp/"+$("#save_emp_edit").	attr("emp_id"),
     data:emp,
     type:"put",
     ...
   }
   //后端
   @ResponseBody
   //springmvc将URL带的参数和传来的参数一起封装给employee，前提是 eId是employee的属性名
   @RequestMapping(value = "/emp/{eId}",method = RequestMethod.PUT)
   public Msg saveEmp(Employee employee){
       System.out.println(employee);
       employeeService.updateEmp(employee);
       return Msg.success();
   }       
   ```

   