---
description: >-
  jwt的类库主要需要掌握完成三件事： jwt密钥的生成 (generateKey) jwt的签名与验证 (sign & verify) jwt的加密与解密
  (encrypt & decrypt)
---

# JWT - Java Lib

## Jose4j

### Jose4j应用

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

Jose4jUtil工具包：

{% code title="Jose4jUtil" %}
```java
```
{% endcode %}

### Jose4j实践

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

## java-jwt

_<mark style="color:blue;">**maven**</mark>_<mark style="color:blue;">**坐标**</mark>



_<mark style="color:blue;">**Wiki**</mark>_

_<mark style="color:blue;">****</mark>_

JavaJwtUtil工具包：

{% code title="JavaJwtUtil" %}
```java
```
{% endcode %}

## JJwt

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

JJwtUtil工具包

{% code title="JJwtUtil" %}
```java
public final class JJWTUtil {

    public static final JwtBuilder hmacJwt;
    public static final JwtParser hmacJwtParser;
    private static final Base64.Encoder encoder = Base64.getEncoder();

    private JJWTUtil() {
    }

    public static String getJws(Map<String, Object> header, Map<String, Object> claims) {
        return hmacJwt
                .setHeader(header)
                .setClaims(claims)
                .compact();
    }

    public static Claims getClaims(String jws) {
        return hmacJwtParser.parseClaimsJws(jws).getBody();
    }

    public static Map<String, Object> getClaimsAsMap(String jws) {
        return getClaims(jws);
    }

    public static String getSecretKey(SignatureAlgorithm algorithm) {
        SecretKey secretKey = Keys.secretKeyFor(algorithm);
        return encoder.encodeToString(secretKey.getEncoded());
    }

    public static Map<String, String> getSecretKeyPair(SignatureAlgorithm algorithm) {
        KeyPair keyPair = Keys.keyPairFor(algorithm);
        return ImmutableMap.of(
                "public", encoder.encodeToString(keyPair.getPublic().getEncoded()),
                "private", encoder.encodeToString(keyPair.getPrivate().getEncoded())
        );
    }

    static {
        hmacJwt = Jwts.builder().signWith(JsonWebConst.getHMACKey());
        hmacJwtParser = Jwts.parserBuilder().setSigningKey(JsonWebConst.getHMACKey()).build();
    }
}
```
{% endcode %}

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

jwt支持密钥及安全算法

```java
/**
 * jjwt支持对称和非对称两种三类：HMAC RSASSA ECDSA
 */
public void generateSecretKey() {
    // HMAC using SHA-512
    Keys.secretKeyFor(SignatureAlgorithm.HS512);
    // ECDSA using P-521 and SHA-512
    Keys.keyPairFor(SignatureAlgorithm.ES512);
    // RSASSA-PKCS-v1_5 using SHA-512
    Keys.keyPairFor(SignatureAlgorithm.RS512);
}
```

