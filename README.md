---
description: 关于spring-boot项目统一controller接口返回值格式
---

# 统一后端接口解决方案

## ReturnValue

```json
{
  "code": "SUC0000",
  "message": "成功",
  "timestamp": 1669374903292,
  "data": null
}
```

| 参数        | 含义      |
| --------- | ------- |
| code      | 接口响应状态码 |
| message   | 接口返回信息  |
| timestamp | 时间戳     |
| data      | 数据      |



利用spring-web带有的控制器增强，@ControllerAdvice，统一包装controller接口的返回值

1. 针对接口成功响应: @ControllerAdvice
2. 针对接口异常响应: @ControllerAdvice + @ExceptionHandler

<figure><img src=".gitbook/assets/image.png" alt=""><figcaption><p>ResponseBodyAdvice接口</p></figcaption></figure>

## Q\&A <a href="#wv96h" id="wv96h"></a>

### 1. 这里涉及到的知识点 <a href="#urzbv" id="urzbv"></a>

答：这里其实就是单纯的API接口（controller）的返回值包装。所以一定设计到http的知识。

需要理解：HttpMessageConverter、Spring的api处理与返回的流程。

### 2. 返回String类型值报错 <a href="#ygwlf" id="ygwlf"></a>

答：这里就是HttpMessageConverter处理流程过程中出现的错误。可参考：[SpringMVC统一封装响应结果接口返回String类型时的异常问题分析](https://blog.csdn.net/m0\_50275872/article/details/125466287)

### 3. Springdoc使用统一包装导致swagger页面无api定义 <a href="#pzovb" id="pzovb"></a>

答：这里主要是统一包装，将springdoc的swagger.json也包装了，格式变化，导致swagger无法解析json，出现页面无api定义！可参考具体实现样例中，关于springdoc的处理。[修复swagger页面展示api无定义](https://github.com/matemaster9/CS98.00\_StandardWebServerConfigCenter/blob/M1/mcc-web/src/main/java/cs/matemaster/standardwebserver/core/ResponseControllerAdviceImpl.java)

## http message的处理流程

