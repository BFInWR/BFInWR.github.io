---
title: SpringBoot 中的登录验证
date: 2019-1-17 19:25:00
categories: "Java"
tags:
	- Java
	- 项目笔记
	- SpringBoot

---
## SpringBoot 中的登录验证

`authoritiesByUsernameQuery(sql)`
传入的sql 需要返回 用户名和 用户级别

源码中的默认sql
```java
private String authoritiesByUsernameQuery = "select username,authority from authorities where username = ?";
```

`usersByUsernameQuery(sql)`
传入的sql 需要返回 用户名,密码,是否启动 enabled = 1 是默认启用
源码中的默认sql
```java
"select username,password,enabled from users where username = ?";
```


另一种验证方案
<!-- more -->
```java
	@Autowired
    AdminUserService adminUserService;

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth
                .userDetailsService(buildUserDetailsService())
                .passwordEncoder(new BCryptPasswordEncoder());
    }

    @Bean
    UserDetailsService buildUserDetailsService() {
        return username -> {
            AdminUser account = adminUserService.selectByUsername(username);
            User user = new User(account.getName(), account.getPasswd(),
                    true, true, true, true,
                    AuthorityUtils.createAuthorityList("USER"));
            return user;
        };
    }
```