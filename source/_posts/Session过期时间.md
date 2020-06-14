---
title: Session过期时间
date: 2020-05-17 22:41:51
categories: 代码实现
tags: JavaWeb
---

![](https://oss.forestyoung.top/lighthouse-5286823_1920.jpg)
<!--more-->

## 背景

项目使用cookie和session实现用户认证，在使用过程中通过设置session的过期时间来达到超时自动退出登录的效果

```java
public class UserController {
 		/**
     * session过期时间15分钟(session默认过期时间为半小时，这里设置15分钟与默认时间区分，防止时间混淆)
     */
    private static final int MAX_TIME = 15 * 60;
  
    /**
     * 用户登录
     * 
     * @param user
     * @return 
     * @author Forest
     * @date 2020/4/28 18:17
    */
    @PostMapping("login")
    public ResultData login(HttpServletRequest request, @RequestBody UserDTO user) {
        ResultData resultData = userService.login(user);
        if (resultData.isSucceed()) {
            // 登录后设置session过期时间半小时
            HttpSession session = request.getSession();
            session.setAttribute("isLogin", "y");
            session.setAttribute("userName", user.getUserName());
            session.setMaxInactiveInterval(MAX_TIME);
            log.info("用户:{}登录成功", user.getUserName());
        }
        return resultData;
    }
}
```

于是猜测是不是可以设置过期时间为0或负数来达到使session失效的效果，从而实现退出登录功能

```java
session.setMaxInactiveInterval(0);
// 或者任意负数
session.setMaxInactiveInterval(-1);
```

debug发现当调用完登出接口后，session依然存在

```java
		/**
     * 用户登出
     *
     * @param
     * @return
     * @author Forest
     * @date 2020/5/17 5:36 下午
     */
    @PostMapping("loginOut")
    public ResultData loginOut(HttpServletRequest request) {
        HttpSession session = request.getSession();
        log.info("用户：{}登出", session.getAttribute("userName"));
        session.setMaxInactiveInterval(0);
        return ResultDataUtils.getSuccessResult("登出成功");
    }
```



## 原因

