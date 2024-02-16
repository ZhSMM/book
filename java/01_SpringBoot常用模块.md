# SpringBoot常用模块
---

- [API接口分离](#api接口分离)
- [全局异常处理](#全局异常处理)


---


#### API接口分离



#### 全局异常处理

Spring Boot全局异常处理流程：

1. 自定义异常类：继承RuntimeException，可以加上异常原因码和异常消息
2. 

Spring Boot默认异常处理类：

+ org.springframework.boot.web.reactive.error.DefaultErrorAttributes（扩展该类的异常处理）
+ org.springframework.boot.autoconfigure.web.servlet.error.BasicErrorController