---
description: 基于guava实现常用hash算法
---

# Hash算法

## 常见hash算法

目前支持hash

| 算法          | 描述 |
| ----------- | -- |
| murmurhash3 |    |
| HMAC SHA1   |    |
| HMAC SHA256 |    |
| HMAC SHA512 |    |
| SipHash24   |    |

## HashUtil

HashUtil旨在提供两种hash结果：

1. int、long类型（及32bit、64bit）的hash code
2. base64编码的hash字符串

```java
public final class HashUtil {

    private static Map<HashAlgorithm, HashFunction> hashFunctionStrategy;
    private static Key hmacSha1Key;
    private static Key hmacSha256Key;
    private static Key hmacSha512Key;
    private static Key hmacMd5Key;
    private static Base64.Encoder encoder = Base64.getEncoder();

    private HashUtil() {
    }

    public static long hmacMd5(@Nonnull Object arg) {
        HashCode hashCode = getHashCode(arg.toString(), HashAlgorithm.HMAC_MD5);
        return hashCode.bits() <= 32 ? hashCode.asInt() : hashCode.asLong();
    }

    public static String hmacMd5AsBae64(@Nonnull Object arg) {
        HashCode hashCode = getHashCode(arg.toString(), HashAlgorithm.HMAC_MD5);
        return encoder.encodeToString(hashCode.asBytes());
    }

    public static long murmurhash3(@Nonnull Object arg) {
        HashCode hashCode = getHashCode(arg.toString(), HashAlgorithm.Murmurhash3_128);
        return hashCode.bits() <= 32 ? hashCode.asInt() : hashCode.asLong();
    }

    public static String murmurhash3AsBase64(@Nonnull Object arg) {
        HashCode hashCode = getHashCode(arg.toString(), HashAlgorithm.Murmurhash3_128);
        return encoder.encodeToString(hashCode.asBytes());
    }

    public static long hmacSHA1(@Nonnull Object arg) {
        HashCode hashCode = getHashCode(arg.toString(), HashAlgorithm.HMAC_SHA1);
        return hashCode.bits() <= 32 ? hashCode.asInt() : hashCode.asLong();
    }

    public static String hmacSHA1AsBase64(@Nonnull Object arg) {
        HashCode hashCode = getHashCode(arg.toString(), HashAlgorithm.HMAC_SHA1);
        return encoder.encodeToString(hashCode.asBytes());
    }

    public static long hmacSHA256(@Nonnull Object arg) {
        HashCode hashCode = getHashCode(arg.toString(), HashAlgorithm.HMAC_SHA256);
        return hashCode.bits() <= 32 ? hashCode.asInt() : hashCode.asLong();
    }

    public static String hmacSHA256AsBase64(@Nonnull Object arg) {
        HashCode hashCode = getHashCode(arg.toString(), HashAlgorithm.HMAC_SHA256);
        return encoder.encodeToString(hashCode.asBytes());
    }

    public static long hmacSHA512(@Nonnull Object arg) {
        HashCode hashCode = getHashCode(arg.toString(), HashAlgorithm.HMAC_SHA512);
        return hashCode.bits() <= 32 ? hashCode.asInt() : hashCode.asLong();
    }

    public static String hmacSHA512AsBase64(@Nonnull Object arg) {
        HashCode hashCode = getHashCode(arg.toString(), HashAlgorithm.HMAC_SHA512);
        return encoder.encodeToString(hashCode.asBytes());
    }

    public static long sipHash24(@Nonnull Object arg) {
        HashCode hashCode = getHashCode(arg.toString(), HashAlgorithm.SIP_HASH24);
        return hashCode.bits() <= 32 ? hashCode.asInt() : hashCode.asLong();
    }

    public static String sipHash24AsBase64(@Nonnull Object arg) {
        HashCode hashCode = getHashCode(arg.toString(), HashAlgorithm.SIP_HASH24);
        return encoder.encodeToString(hashCode.asBytes());
    }

    private static HashCode getHashCode(String content, HashAlgorithm algorithm) {
        HashFunction hashFunc = hashFunctionStrategy.get(algorithm);
        return hashFunc.hashBytes(content.getBytes(StandardCharsets.UTF_8));
    }

    public enum HashAlgorithm {
        Murmurhash3_128,
        HMAC_SHA1,
        HMAC_SHA256,
        HMAC_SHA512,
        HMAC_MD5,
        SIP_HASH24
    }

    static {
        try {
            hmacMd5Key = KeyGenerator.getInstance("HmacMD5").generateKey();
            hmacSha1Key = KeyGenerator.getInstance("HmacSHA1").generateKey();
            hmacSha256Key = KeyGenerator.getInstance("HmacSHA256").generateKey();
            hmacSha512Key = KeyGenerator.getInstance("HmacSHA512").generateKey();
        } catch (NoSuchAlgorithmException ignore) {

        }
    }

    static {
        hashFunctionStrategy = ImmutableMap.<HashAlgorithm, HashFunction>builder()
                .put(HashAlgorithm.Murmurhash3_128, Hashing.murmur3_128())
                .put(HashAlgorithm.HMAC_MD5, Hashing.hmacMd5(hmacMd5Key))
                .put(HashAlgorithm.HMAC_SHA1, Hashing.hmacSha1(hmacSha1Key))
                .put(HashAlgorithm.HMAC_SHA256, Hashing.hmacSha256(hmacSha256Key))
                .put(HashAlgorithm.HMAC_SHA512, Hashing.hmacSha512(hmacSha512Key))
                .put(HashAlgorithm.SIP_HASH24, Hashing.sipHash24())
                .build();
    }
}
```
