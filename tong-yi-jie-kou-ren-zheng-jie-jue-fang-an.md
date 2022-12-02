---
description: 基于JsonWebToken实现后端接口统一认证
---

# 统一接口认证解决方案

## JsonWebToken

### jwt

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



### jws



### jwe

