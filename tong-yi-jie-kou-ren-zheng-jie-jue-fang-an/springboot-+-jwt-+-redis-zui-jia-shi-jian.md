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

* 数据库客户信息验证

```java
public void validate(SysUserDto sysUserDto) {
    SysUserDto sysUser = sysUserMapper.getSysUserByUsername(sysUserDto.getUsername());
    if (sysUser == null) {
        throw new IllegalParamsException("用户不存在");
    }

    if (!sysUserDto.getPassword().equals(sysUser.getPassword())) {
        throw new IllegalParamsException("用户信息不匹配");
    }
}
```

* 签名token并存入redis

```java
public String issueToken(SysUserDto sysUserDto) {
        // jwt标准负载（30 min有效期）
        ImmutableMap<String, Object> accessPayload = ImmutableMap.<String, Object>builder()
                .put("jti", sysUserDto.getUsername())
                .put("iss", "mcc")
                .put("exp", DateTimeUtil.convertLocalDateTimeToDate(LocalDateTime.now().plusMinutes(30L)))
                .put("iat", DateTimeUtil.convertLocalDateTimeToDate(LocalDateTime.now()))
                .put("nbf", DateTimeUtil.convertLocalDateTimeToDate(LocalDateTime.now()))
                .put("SysUser", Objects.requireNonNull(JsonUtil.serialize(sysUserDto)))
                .build();
        // jwt标准负载（3 h有效期）
        ImmutableMap<String, Object> refreshPayload = ImmutableMap.<String, Object>builder()
                .put("jti", sysUserDto.getUsername())
                .put("iss", "mcc")
                .put("exp", DateTimeUtil.convertLocalDateTimeToDate(LocalDateTime.now().plusHours(3L)))
                .put("iat", DateTimeUtil.convertLocalDateTimeToDate(LocalDateTime.now()))
                .put("nbf", DateTimeUtil.convertLocalDateTimeToDate(LocalDateTime.now()))
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

* **token的签名与验证**

```java
public class JJwtImpl extends AbstractJsonWebTokenSupport implements JsonWebTokenSupport {

    private JsonWebProperties jsonWebProperties;

    private static final Map<String, Object> HEADER = Maps.<String, Object>of("alg", "HS512")
            .and("typ", "JWT")
            .build();

    @Override
    public String sign(Map<String, Object> payload) {
        // 签名密钥可以自行隐藏在配置文件
        return Jwts.builder()
                .setHeader(HEADER)
                .setClaims(payload)
                .signWith(jsonWebProperties.getHS512Key())
                .compact();
    }

    @Override
    public Map<String, Object> verify(String jws) {
        return super.verify(jws);
    }
}
```

## Auth切面
