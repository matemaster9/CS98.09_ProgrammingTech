---
description: 基于JWT + Redis实现token验证方案
---

# SpringBoot + JWT + Redis最佳实践

## token的生成策略

对象设计：

```java
@Setter
@Getter
public class SysToken {
    private String accessToken;
    private String refreshToken;
}
```

定义accessToken与refreshToken实现token续签无感知。

accessToken有效时间为三十分钟、refreshToken有效时间为三个小时

token负载中除了标准声明之外，还需加入系统用户信息（username、passowrd……后续可再加）

## 接口认证流程

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption><p>refresh策略</p></figcaption></figure>

签发token接口设计：

```java
@PostMapping("getToken")
public String getToken(@RequestBody SysUserDto request) {
    return signatureFacade.getToken(request);
}
```

签发的业务流程

```java
public String getToken(SysUserDto sysUserDto) {
    sysUserService.validate(sysUserDto);
    return signatureService.issueToken(sysUserDto);
}
```

```java
public String issueToken(SysUserDto sysUserDto) {
        ImmutableMap<String, Object> accessPayload = ImmutableMap.<String, Object>builder()
                .put("jti", "")
                .put("exp", new Date())
                .put("iat", new Date())
                .put("iss", "mcc")
                .put("nbf", new Date())
                .put("SysUser", Objects.requireNonNull(JsonUtil.serialize(sysUserDto)))
                .build();
        
        ImmutableMap<String, Object> refreshPayload = ImmutableMap.<String, Object>builder()
                .put("jti", "")
                .put("exp", new Date())
                .put("iat", new Date())
                .put("iss", "mcc")
                .put("nbf", new Date())
                .put("SysUser", Objects.requireNonNull(JsonUtil.serialize(sysUserDto)))
                .build();
        
        String accessToken = jsonWebTokenSupport.sign(accessPayload);
        String refreshToken = jsonWebTokenSupport.sign(refreshPayload);
        
        SysToken sysToken = new SysToken();
        sysToken.setAccessToken(accessToken);
        sysToken.setRefreshToken(refreshToken);
        
        redisClientSupport.setExpiredMessage("SysToken", (String) refreshPayload.get("jti"), JsonUtil.serialize(sysToken), 1000);
        return accessToken;
}
```

## token服务实现

### 签名token



### 验证token



## 签名切面
