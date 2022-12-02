---
description: 基于JsonWebToken实现后端接口统一认证
---

# 统一接口认证解决方案

## JsonWebToken

### JWT

JsonWebToken主体分为三个部分：header、payload、signature

* header

```json
{
    "alg": "HS256",
    "typ": "JWT",
    "cty": ""
}
```

| 参数  | 含义           |
| --- | ------------ |
| alg | jwt的签名或解密的算法 |
| typ | 一般设置为JWT     |
| cty | 针对嵌套的jwt可使用  |

* payload

有效负载一般分为两类：用户自定义、标准注册负载（iss、sub、aud、exp、nbf、iat、jti）

| 参数             |     | 含义                     |
| -------------- | --- | ---------------------- |
| issuer         | iss | jwt的发行者                |
| subject        | sub | jwt信息的主题               |
| audience       | aud | jwt的接受者信息              |
| expire         | exp | jwt的过期时间，以1970年纪元以来的秒数 |
| not before     | nbf | jwt的生效时间               |
| issued at time | iat | jwt的发行时间               |
| jwt id         | jti | jwt的唯一id               |

```json
{
  "jti": "4a9813f957b84dda8091510402e7c33d",
  "iss": "MateMaster",
  "sub": "有效负载测试",
  "aud": "audience",
  "exp": 1670054958,
  "nbf": 1669968618,
  "iat": 1669968558
}
```

### JWS

JWS(Signed JWT)主要生成流程：

<figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption><p>JWS Compact Serialization</p></figcaption></figure>

<mark style="color:red;">**难点**</mark>：

1. 什么是JWS JSON Serialization?
2. JWS header claims的各个参数作用？

<mark style="color:red;">**重点知识**</mark>：

在JWS中，Share SecretKey，各方都可以验证和生成令牌。在公钥/私钥中，_只有私钥可以验证、生成令牌_，_公钥只可以验证令牌，不能用于生成令牌_

_**从生产者与消费者角度理解JWS**_

| 角色  | JWS         |
| --- | ----------- |
| 生产者 | private key |
| 消费者 | public key  |

<figure><img src=".gitbook/assets/image (2).png" alt=""><figcaption><p>JWT 签名</p></figcaption></figure>

### JWE



