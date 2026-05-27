---
created: 2026-05-21
tags:
  - java/internship
  - java/ssm
related:
  - "[[../ssm]]"
  - "[[spring]]"
  - "[[mybatis]]"
---

# SpringMVC

**SpringMVC = Web 层框架，替代 Servlet**

## 执行流程

```
用户请求 → DispatcherServlet（前端控制器）
    → HandlerMapping（找哪个 Controller 处理）
    → Controller（执行业务逻辑）
    → ModelAndView（封装数据和视图）
    → ViewResolver（解析视图）
    → 响应页面 / JSON
```

## 常用注解

```java
@Controller
@RequestMapping("/user")   // 类级别：统一前缀
public class UserController {

    @RequestMapping(value = "/list", method = RequestMethod.GET)
    // @GetMapping("/list")    // 简化写法
    @ResponseBody             // 返回 JSON，不走视图解析器
    public String list() {
        return "user list";
    }
}
```

### 请求注解

| 注解 | 说明 |
| ---- | ---- |
| `@RequestMapping` | 映射请求路径（可加 method） |
| `@GetMapping` | GET 请求 |
| `@PostMapping` | POST 请求 |
| `@PutMapping` | PUT 请求 |
| `@DeleteMapping` | DELETE 请求 |

### 参数绑定

```java
// 1. 普通参数（参数名一致自动匹配）
@GetMapping("/get")
@ResponseBody
public String get(String name, int age) {}

// 2. @RequestParam（参数名不一致 / 必填）
@GetMapping("/get")
@ResponseBody
public String get(@RequestParam("username") String name,
                  @RequestParam(required = false) Integer age) {}

// 3. @PathVariable（RESTful）
@GetMapping("/user/{id}")
@ResponseBody
public String get(@PathVariable Integer id) {}

// 4. @RequestBody（JSON）
@PostMapping("/save")
@ResponseBody
public String save(@RequestBody User user) {}
```

## 响应

```java
@RestController            // = @Controller + @ResponseBody
@RequestMapping("/user")
public class UserController {

    @GetMapping("/{id}")
    public Result<User> getById(@PathVariable Integer id) {
        User user = userService.getById(id);
        return Result.success(user);   // 统一返回 JSON
    }
}
```

> 前后端分离项目：统一返回 `Result` 对象（code + message + data）

## 拦截器（Interceptor）

```java
@Component
public class LoginInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        // 在 Controller 执行前运行
        // 返回 true 放行，false 拦截
        return true;
    }

    @Override
    public void postHandle(...) {}      // Controller 执行后，视图渲染前

    @Override
    public void afterCompletion(...) {} // 视图渲染后
}
```

```java
// 注册拦截器
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginInterceptor())
                .addPathPatterns("/**")
                .excludePathPatterns("/login", "/register");
    }
}
```

> **拦截器 vs 过滤器**：拦截器是 SpringMVC 的，只拦截 Controller 请求；过滤器是 Servlet 的，范围更广

---

**相关笔记**: [[../ssm]] · [[spring]] · [[mybatis]]
