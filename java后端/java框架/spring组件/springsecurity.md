# 前端页面结构

![image-20201218211539323](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201218211539323.png)

![image-20201218211558033](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201218211558033.png)

# 配置类

```java
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    //定制请求的授权规则
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        //限制角色的访问
        http.authorizeRequests().antMatchers("/").permitAll()
                .antMatchers("/view1/**").hasRole("vip1")
                .antMatchers("/view2/**").hasRole("vip2")
                .antMatchers("/view3/**").hasRole("vip3");

        // /login 请求来到登录页
        // /login?error 重定向到这里表示登录失败
        //开启自动配置的登录功能：如果没有权限，就会跳转到登录页面；
        http.formLogin().
                //获取自定义表单中的账号和密码
                usernameParameter("username")
                .passwordParameter("password")
                // loginPage("/toLogin")是访问自定义登陆页面的请求
                .loginPage("/toLogin")
                // 登陆表单提交请求，前端页面上登陆按钮跳转的URL也要是login
                .loginProcessingUrl("/login");

        //注销成功后来到首页
        http.logout().logoutSuccessUrl("/");
        //记住我；rememberMeParameter("remember") 是自定义登陆页面时使用的
        http.rememberMe().rememberMeParameter("remember");
    }

    //定义认证规则
    protected void configure(AuthenticationManagerBuilder auth) throws Exception{
        //用户的认证和授权信息可以在内存中定义，也可以在jdbc中去拿....
        //Spring security 5.0中新增了多种加密方式，也改变了密码的格式。
        //要想我们的项目还能够正常登陆，需要修改一下configure中的代码。我们要将前端传过来的密码进行某种方式加密
        //spring security 官方推荐的是使用bcrypt加密方式。
        auth.inMemoryAuthentication().passwordEncoder(new BCryptPasswordEncoder())
                .withUser("root").password(new BCryptPasswordEncoder().encode("root")).roles("vip1","vip2","vip3").and()
                .withUser("zhangsan").password(new BCryptPasswordEncoder().encode("zs")).roles("vip1").and()
                .withUser("lisi").password(new BCryptPasswordEncoder().encode("ls")).roles("vip2").and()
                .withUser("wangwu").password(new BCryptPasswordEncoder().encode("ww")).roles("vip3");
    }


}
```

