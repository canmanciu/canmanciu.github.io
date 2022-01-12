---
title: Base64
---
### 什么是Base64
[维基百科](https://en.wikipedia.org/wiki/Base64) 是如此定义的
>  In computer programming, Base64 is a group of binary-to-text encoding schemes that represent binary data (more specifically, a sequence of 8-bit bytes) in an ASCII string format by translating the data into a radix-64 representation. The term Base64 originates from a specific MIME content transfer encoding. Each non-final Base64 digit represents exactly 6 bits of data. Three bytes (i.e., a total of 24 bits) can therefore be represented by four 6-bit Base64 digits.


一句话描述：Base64 是一种用于表示二进制数据的编码方案


### 相关标准
- [The Base16, Base32, and Base64 Data Encodings](https://www.ietf.org/rfc/rfc4648.txt)
- [Multipurpose Internet Mail Extensions](https://www.ietf.org/rfc/rfc2045.txt)


### JAVA 实现
1. JDK：由于jdk8的实现是比较完整的，所以只讨论该部分

- 实现描述 java.util.Base64
> This class consists exclusively of static methods for obtaining encoders and decoders for the Base64 encoding scheme. The implementation of this class supports the following types of Base64 as specified in RFC 4648  and RFC 2045 .
> 1. Basic：Uses "The Base64 Alphabet" as specified in Table 1 of RFC 4648 and RFC 2045 for encoding and decoding operation. The encoder does not add any line feed (line separator) character. The decoder rejects data that contains characters outside the base64 alphabet.
> 2. URL and Filename safe：Uses the "URL and Filename safe Base64 Alphabet" as specified in Table 2 of RFC 4648 for encoding and decoding. The encoder does not add any line feed (line separator) character. The decoder rejects data that contains characters outside the base64 alphabet.
> 3. MIME：Uses the "The Base64 Alphabet" as specified in Table 1 of RFC 2045 for encoding and decoding operation. The encoded output must be represented in lines of no more than 76 characters each and uses a carriage return '\r' followed immediately by a linefeed '\n' as the line separator. No line separator is added to the end of the encoded output. All line separators or other characters not found in the base64 alphabet table are ignored in decoding operation.

- 内部实现详情
``` JAVA
// 编码表
/**
 * This array is a lookup table that translates 6-bit positive integer
 * index values into their "Base64 Alphabet" equivalents as specified
 * in "Table 1: The Base64 Alphabet" of RFC 2045 (and RFC 4648).
 */
private static final char[] toBase64 = {
    'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M',
    'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z',
    'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm',
    'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z',
    '0', '1', '2', '3', '4', '5', '6', '7', '8', '9', '+', '/'
};
/**
 * It's the lookup table for "URL and Filename safe Base64" as specified
 * in Table 2 of the RFC 4648, with the '+' and '/' changed to '-' and
 * '_'. This table is used when BASE64_URL is specified.
 */
private static final char[] toBase64URL = {
    'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M',
    'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z',
    'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm',
    'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z',
    '0', '1', '2', '3', '4', '5', '6', '7', '8', '9', '-', '_'
};

// 使用入口
/**
 * Returns a {@link Decoder} that decodes using the
 * <a href="#basic">Basic</a> type base64 encoding scheme.
 *
 * @return  A Base64 decoder.
 */
public static Decoder getDecoder() {
     return Decoder.RFC4648;
}
/**
 * Returns a {@link Decoder} that decodes using the
 * <a href="#url">URL and Filename safe</a> type base64
 * encoding scheme.
 *
 * @return  A Base64 decoder.
 */
public static Decoder getUrlDecoder() {
     return Decoder.RFC4648_URLSAFE;
}
/**
 * Returns a {@link Decoder} that decodes using the
 * <a href="#mime">MIME</a> type base64 decoding scheme.
 *
 * @return  A Base64 decoder.
 */
public static Decoder getMimeDecoder() {
     return Decoder.RFC2045;
}
```

2. Apache commons-codec： apache 也同时实现了 urlsafe 和标准的 Base64
``` xml
<!-- maven 依赖 -->
<dependency>
    <groupId>commons-codec</groupId>
    <artifactId>commons-codec</artifactId>
    <version>1.15</version>
</dependency>
```
``` JAVA
// 使用入口
// encode
public static byte[] encodeBase64(byte[] binaryData, boolean isChunked, boolean urlSafe)
// decode
public static byte[] decodeBase64(String base64String)
public static byte[] decodeBase64(byte[] base64Data)
```

对比JDK和Apache提供的实现，Apache 的解码使用的是统一的一种方式，兼容性更强，无论是哪种方式编码都可以处理