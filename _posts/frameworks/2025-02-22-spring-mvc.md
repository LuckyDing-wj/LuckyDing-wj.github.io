---
layout: post
title: "Spring MVC 入门与请求处理"
date: 2025-02-22 22:05:00 +0800
categories: [常用框架]
tags: [Spring, Spring MVC, Web]
---

## 一、Spring MVC 是什么

Spring MVC 是 Spring 提供的 Web 框架，基于 MVC 模式，负责处理 HTTP 请求与响应。

---

## 二、核心组件

- `DispatcherServlet`：前端控制器
- `HandlerMapping`：请求与处理器映射
- `Controller`：业务控制器
- `ViewResolver`：视图解析

---

## 三、基础示例

```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    @GetMapping("/{id}")
    public User find(@PathVariable Long id) {
        return userService.findById(id);
    }
}
```

---

## 四、常用注解

- `@Controller` / `@RestController`
- `@RequestMapping` / `@GetMapping` / `@PostMapping`
- `@RequestParam` / `@PathVariable` / `@RequestBody`
- `@ResponseBody`

---

## 五、参数绑定与校验

```java
public class CreateUserRequest {
    @NotBlank
    private String username;
}

@PostMapping
public User create(@Valid @RequestBody CreateUserRequest req) {
    return userService.create(req);
}
```

---

## 六、异常处理

```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(BizException.class)
    public ErrorResponse handle(BizException ex) {
        return new ErrorResponse(ex.getMessage());
    }
}
```

---

## 七、学习建议

- 理解 `DispatcherServlet` 的请求流程
- 熟悉注解与参数绑定
- 配合 Spring Boot 实战

---

## 八、学习资源

- [Spring MVC 官方文档](https://docs.spring.io/spring-framework/reference/web/webmvc.html)
- [Spring MVC 示例 - Spring Guides](https://spring.io/guides/gs/serving-web-content/)

---

## 相关链接

- [学习资源汇总](/2025/02/22/learning-resources.html)
- [返回学习路线](/#roadmap)

---

*持续更新中...*

