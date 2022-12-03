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

| 参数       | 含义                                                                                                                                  |
| -------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| jku      | JSON Web 密钥 (JWK) 设置 URL。指向一组用于签署此 JWT 的 JSON 编码公钥的 URI。必须使用传输安全（例如用于 HTTP 的 TLS）来检索密钥。密钥的格式是 JWK 集                                 |
| jwk      | JSON Web 密钥。用于以 JSON Web 密钥格式签署此 JWT 的密钥                                                                                            |
| kid      | 密钥ID。一个用户定义的字符串，表示用于签署此 JWT 的单个密钥。此声明用于向收件人发出密钥签名更改信号（当使用多个密钥时）                                                                     |
| x5u      | X.509 网址。指向一组以 PEM 形式编码的 X.509（证书格式标准）公共证书的 URI。该集中的第一个证书必须是用于签署此 JWT 的证书。后续证书分别签署前一个证书，从而完成证书链。 X.509 在 RFC 5280 7 中定义。传输证书需要传输安全性 |
| x5c      | X.509 证书链。用于签署此 JWS 的 X.509 证书的 JSON 数组。每个证书必须是其 DER PKIX 表示的 Base64 编码值。数组中的第一个证书必须是用于签署此 JWT 的证书，然后是证书链中的其余证书                     |
| x5t      | X.509 证书SHA-1 指纹。用于签署此 JWT 的 X.509 DER 编码证书的 SHA-1 指纹                                                                               |
| x5t#S256 | 与x5t 相同，但使用SHA-256 而不是SHA-1                                                                                                         |
| typ      | 与未加密 JWT 的 typ 值相同，附加值“JOSE”和“JOSE+JSON”分别用于指示紧凑序列化和 JSON 序列化。这仅用于类似 JOSE-header 携带对象与此 J​​WT 在单个容器中混合的情况                           |
| crit     | 来自critical。包含声明名称的字符串数组，这些声明名称出现在同一标头中，用作必须由此 JWT 的解析器处理的实现定义的扩展。它必须包含声明的名称或不存在（空数组不是有效值）。                                          |

<mark style="color:red;">**重点知识**</mark>：

在JWS中，Share SecretKey，各方都可以验证和生成令牌。在公钥/私钥中，_只有私钥可以验证、生成令牌_，_公钥只可以验证令牌，不能用于生成令牌_

_**从生产者与消费者角度理解JWS**_

| 角色  | JWS         |
| --- | ----------- |
| 生产者 | private key |
| 消费者 | public key  |

<figure><img src=".gitbook/assets/image (2) (1).png" alt=""><figcaption><p>JWT 签名与验证</p></figcaption></figure>

JWS的签名算法：

{% tabs %}
{% tab title="HMAC" %}
HS256: HMAC using SHA-256

HS384: HMAC using SHA-384

HS512: HMAC using SHA-512
{% endtab %}

{% tab title="RSA" %}
RS256: RSASSA-PKCS-v1\_5 using SHA-256

RS384: RSASSA-PKCS-v1\_5 using SHA-384

RS512: RSASSA-PKCS-v1\_5 using SHA-512

PS256: RSASSA-PSS using SHA-256 and MGF1 with SHA-256

PS384: RSASSA-PSS using SHA-384 and MGF1 with SHA-384

PS512: RSASSA-PSS using SHA-512 and MGF1 with SHA-512
{% endtab %}

{% tab title="ECDSA" %}
ES256: ECDSA using P-256 and SHA-256

ES384: ECDSA using P-384 and SHA-384

ES512: ECDSA using P-512 and SHA-512
{% endtab %}
{% endtabs %}

### JWE

JWE(Encrypted JWT)compact序列化主要生成流程：

1. 根据alg声明算法，生成所需的随机数
2. 依据密钥管理方式确定CEK
3. 依据密钥管理方式确定JWE Encrypt Key
4. 计算初始化向量（如果需要）
5. 压缩文本内容（如果需要）
6. 使用CEK、IV或AAD加密数据

JWE(Encrypted JWT)compact序列化组成部分：

> base64(header)&#x20;
>
> base64(encryptedKey) _\[step 2,3]_
>
> base64(initializationVector) _\[step 4]_
>
> base64(cipherText) _\[step 6]_
>
> base64(authenticationTag) _\[step 6]_

```vim
eyJhbGciOiJBMTI4S1ciLCJlbmMiOiJBMTI4Q0JDLUhTMjU2In0.
Y2DxdVnvuDwo5vutvvPg4PpGQKFmRxWoDUCtfs58Gv5rJ4J1RkSOUQ.
-Iu2VusgO_w0uWrn0JWx3Q.
krW8miBqh5x3dZ6ktf0C_A.
HHYK0TxHth2949NDPpwTsw
```

<mark style="color:red;">**重点知识**</mark>：

在JWE中，Share SecretKey，各方都可以加密解密令牌。在对称加密中，只有公钥可以加密数据，私钥解密

_**从生产者与消费者角度理解JWE**_

| 角色  | JWE         |
| --- | ----------- |
| 生产者 | public key  |
| 消费者 | private key |

<figure><img src=".gitbook/assets/image (2).png" alt=""><figcaption><p>jwt 加密与解密</p></figcaption></figure>



### JWK

JWK的出现旨在，为不同加密密钥提供一个统一的格式标准

JWK样例

```json
{
    "kty": "EC",
    "crv": "P-256",
    "x": "MKBCTNIcKUSDii11ySs3526iDZ8AiTo7Tu6KPAqv7D4",
    "y": "4Etl6SRW2YiLUrN5vfvVHuhp7x8PxltmWWlbbM4IFyM",
    "d": "870MB6gfuTJ4HtUnUvYMyJpr5eUZNP4Bk43bVdj3eAE",
    "use": "enc",
    "kid": "1"
}
```

| 参数       | 含义       | sample     |
| -------- | -------- | ---------- |
| kty      | 密钥类型     | EC、RSA、oct |
| use      | 密钥用途     | sign、enc   |
| key\_ops | 密钥的详细用途  |            |
| alg      | 密钥算法     |            |
| kid      | 密钥的唯一标识符 |            |
| x5u      |          |            |
| x5c      |          |            |
| x5t      |          |            |
| x5t#S256 |          |            |

### JWA



## JWT类库

jwt的类库主要需要掌握完成三件事：

1. jwt密钥的生成 (generateKey)
2. jwt的签名与验证 (sign & verify)
3. [jwt的加密与解密 (encrypt & decrypt)](#user-content-fn-1)[^1]

### jose4j

_<mark style="color:blue;">****</mark>_[_<mark style="color:blue;">**maven**</mark>_<mark style="color:blue;">**坐标**</mark>](https://mvnrepository.com/artifact/org.bitbucket.b\_c/jose4j)<mark style="color:blue;">****</mark>

```xml
<dependency>
    <groupId>org.bitbucket.b_c</groupId>
    <artifactId>jose4j</artifactId>
    <version>${jose4j.version}</version>
</dependency>
```

_<mark style="color:blue;">**Wiki**</mark>_

{% embed url="https://bitbucket.org/b_c/jose4j/wiki/Home" %}
jose4j wiki
{% endembed %}

_**jwe生成**_

```java
@Test
public void encryptJwt() throws JoseException {
    AesKey key = new AesKey(ByteUtil.randomBytes(16));
    JsonWebEncryption encryptedJwt = new JsonWebEncryption();
    encryptedJwt.setPayload("Hello JWE");
    encryptedJwt.setAlgorithmHeaderValue(KeyManagementAlgorithmIdentifiers.A128KW);
    encryptedJwt.setEncryptionMethodHeaderParameter(ContentEncryptionAlgorithmIdentifiers.AES_128_CBC_HMAC_SHA_256);
    encryptedJwt.setKey(key);
    String compactSerialization = encryptedJwt.getCompactSerialization();
    log.debug(compactSerialization);
}
```

### java-jwt





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

## SpringBoot统一接口token认证





[^1]: 部分类库支持
