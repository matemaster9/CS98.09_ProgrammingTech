---
description: 基于JsonWebToken实现后端接口统一认证
---

# 统一接口认证解决方案

## JsonWebToken

关于JsonWebToken的专业名词解释：

* unsecured JWT：默认头部{"alg": "none"}的jwt令牌
* JWS(Signed JWT)：已签名的jwt,包含标准jwt结构：header、payload、signature
* JWE(Encrypted JWT)：已加密的jwt，结构为：
* JWA：所涉及的密码学算法
* JWK：密码学算法所需的密钥

### JWT

> JsonWebToken主体分为三个部分：header、payload、signature

unsecured JWT结果示例：

```vim
eyJhbGciOiJub25lIn0.
eyJqdGkiOiJkZWJhNzhiZDZiNTI0ZTA2OWE4MmZjZTJlNzdmOTU2MSIsImlzcyI6Ik1hdGVNYXN0ZXIiLCJzdWIiOiLmnInmlYjotJ_ovb3mtYvor5UiLCJhdWQiOiJhdWRpZW5jZSIsImV4cCI6MTY3MDExNzIzMywibmJmIjoxNjcwMDMwODkzLCJpYXQiOjE2NzAwMzA4MzN9.
```

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

JWS(Signed JWT)compact序列化主要生成流程：

<figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption><p>JWS Compact Serialization</p></figcaption></figure>

**结果示例**

```vim
eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.
eyJpc3MiOiJtYXRlbWFzdGVyIiwic3ViIjoiandzIiwiYXVkIjoiYXVkaWVuY2UiLCJleHAiOjE2NzAwMzQ4NjEsImlhdCI6MTY3MDAzNDgwMSwibmJmIjoxNjcwMDM0ODAxLCJqdGkiOiIzNTA3M2FhYmMxMmU0ZDUzOTBkNGNjOGFlYzVhNmVlYyJ9.
pKhj-QPDszduhet_SZW6BfsLX0n88D__YvIHvfj87OSitCRq1ybb7Bc3ClZZfhf_ewgrhVUL4d4WK0JuCkl0gA
```

<mark style="color:red;">**难点**</mark>：

1. 什么是JWS JSON Serialization?
2. JWS header claims的各个参数(除标准jwt之外的参数)作用？

#### JWS JSON序列化

JWS JSON 序列化形式（多个签名）

```json
{
    "payload": "eyJpc3MiOiJqb2UiLA0KICJleHAiOjEzMDA4MTkzODAsDQogIm h0dHA6Ly9leGFtcGxlLmNvbS9pc19yb290Ijp0cnVlfQ",
    "signatures": [
        {
            "protected": "eyJhbGciOiJSUzI1NiJ9",
            "header": {
                "kid": "2010-12-29"
            },
            "signature": "cC4hiUPoj9Eetdgtv3hF80EGrhuB__dzERat0XF9g2VtQgr9PJbu3XOiZj5RZmh7AA uHIm4Bh-0Qc_lF5YKt_O8W2Fp5jujGbds9uJdbF9CUAr7t1dnZcAcQjbKBYNX4BAyn RFdiuB--f_nZLgrnbyTyWzO5vRK5h6xBArLIARNPvkSjtQBMHlb1L07Qe7K0GarZRmB _eSN9383LcOLn6_dO--xi12jzDwusC-eOkHWEsqtFZESc6BfI7noOPqvhJ1phCnvWh6 IeYI2w9QOYEUipUTI8np6LbgGY9Fs98rqVt5AXLIhWkWywlVmtVrBp0igcN_IoypGlU PQGe77Rw"
        },
        {
            "protected": "eyJhbGciOiJFUzI1NiJ9",
            "header": {
                "kid": "e9bc097a-ce51-4036-9562-d2ade882db0d"
            },
            "signature": "DtEhU3ljbEg8L38VWAfUAqOyKAM6-Xx-F4GawxaepmXFCgfTjDx w5djxLa8ISlSApmWQxfKTUJqPP3-Kg6NU1Q"
        }
    ]
}
```

扁平化JWS JSON 序列化形式（单个签名）

```json
{
    "payload": "eyJpc3MiOiJqb2UiLA0KICJleHAiOjEzMDA4MTkzODAsDQog Imh0dHA6Ly9leGFtcGxlLmNvbS9pc19yb290Ijp0cnVlfQ",
    "protected": "eyJhbGciOiJFUzI1NiJ9",
    "header": {
        "kid": "e9bc097a-ce51-4036-9562-d2ade882db0d"
    },
    "signature": "DtEhU3ljbEg8L38VWAfUAqOyKAM6-Xx-F4GawxaepmXFC gfTjDxw5djxLa8ISlSApmWQxfKTUJqPP3-Kg6NU1Q"
}
```

JSON 序列化与 compact序列化的区别：

1. Json序列化结果为标准的json格式对象包含：payload、signatures 而compact序列化结果为 header.payload.signature
2. json序列化支持两种header：protected和unprotected，compact只支持protected。ps：protected与unprotected代表这个header是否被签名验证
3. json序列化支持多个签名，每个签名内容包含在signatures数组

JWS JSON字段含义

payload：base64编码的JWT负载字符串

protected：base64编码的JWS头部字符串，包含的声明受到签名保护

signatures：签名数组，header：不受签名保护的header，在unprotected header中是必须的，在protected中是可选的，signature：base64编码的JWS签名字符串

#### JWS 头部声明



<mark style="color:red;">**重点知识**</mark>：

在JWS中，Share SecretKey，各方都可以验证和生成令牌。在公钥/私钥中，_只有私钥可以验证、生成令牌_，_公钥只可以验证令牌，不能用于生成令牌_

_**从生产者与消费者角度理解JWS**_

| 角色  | JWS         |
| --- | ----------- |
| 生产者 | private key |
| 消费者 | public key  |

<figure><img src=".gitbook/assets/image (2).png" alt=""><figcaption><p>JWT 签名</p></figcaption></figure>



### JWE



### JWK



### JWA



## JWT类库

### jjwt

_<mark style="color:blue;">**maven**</mark>_<mark style="color:blue;">**坐标**</mark>

{% code title="jjwt" %}
```xml
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-api</artifactId>
    <version>${jjwt.version}</version>
</dependency>

<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-impl</artifactId>
    <version>${jjwt.version}</version>
</dependency>

<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-jackson</artifactId>
    <version>${jjwt.version}</version>
</dependency>
```
{% endcode %}

_<mark style="color:blue;">**GitHub**</mark>_<mark style="color:blue;">**教程**</mark>

{% embed url="https://github.com/jwtk/jjwt" %}
jjwt GitHub仓库
{% endembed %}

_**jwt的签名和验证**_

```java
@Test
public void signAndValidate() {
        // 颁布密钥 HMAC系列（HS512）
        SecretKey secretKey = Keys.secretKeyFor(SignatureAlgorithm.HS512);
        
        // 标准JWS
        String hs512Jws = Jwts.builder()
                .setHeader(ImmutableMap.<String, Object>builder()
                        .put("alg", "HS512")
                        .put("typ", "JWT")
                        .build())
                .setClaims(ImmutableMap.<String, Object>builder()
                        .put("iss", "matemaster")
                        .put("sub", "jws")
                        .put("aud", "audience")
                        .put("exp", Date.from(LocalDateTime.now().plusMinutes(1).atZone(ZoneId.systemDefault()).toInstant()))
                        .put("iat", Date.from(LocalDateTime.now().atZone(ZoneId.systemDefault()).toInstant()))
                        .put("nbf", Date.from(LocalDateTime.now().atZone(ZoneId.systemDefault()).toInstant()))
                        .put("jti", UUID.randomUUID().toString().replace("-", ""))
                        .build())
                .signWith(secretKey)
                .compact();
        
        // 解析JWS
        Jws<Claims> claimsJws = Jwts
                .parserBuilder()
                .setSigningKey(secretKey)
                .build()
                .parseClaimsJws(hs512Jws);
        
        log.debug(claimsJws.getSignature());
        log.debug(claimsJws.getBody().toString());
        log.debug(claimsJws.getHeader().toString());
}
```

### java-jwt



## SpringBoot统一接口token认证



