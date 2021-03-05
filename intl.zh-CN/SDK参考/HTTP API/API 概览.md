# API 概览

本文介绍 ACM API 的概要信息，包括 API 列表、获取服务器 IP 的方法、通信协议、请求方法、公共参数、签名算法等。

## 配置管理 API

您可以使用本文档介绍的 API 对应用配置服务进行相关操作。请确保在使用这些接口前，您已充分了解 ACM 产品说明和使用协议。

|API|描述|
|---|--|
|[getConfig](/intl.zh-CN/SDK参考/HTTP API/getConfig.md)|获取 ACM 配置内容。|
|[getAllConfigByTenant](/intl.zh-CN/SDK参考/HTTP API/getAllConfigByTenant.md)|获取指定命名空间内的 ACM 配置信息。|
|[addListener](/intl.zh-CN/SDK参考/HTTP API/addListener.md)|监听 ACM 配置的变更。|
|[syncUpdateAll](/intl.zh-CN/SDK参考/HTTP API/syncUpdateAll.md)|发布 ACM 配置。|
|[deleteAllDatums](/intl.zh-CN/SDK参考/HTTP API/deleteAllDatums.md)|删除 ACM 配置。|

## 获取服务器 IP 列表

通过[地域和接入点列表](/intl.zh-CN/SDK参考/SDK简介.md)获取服务 IP 列表，以便通过服务 IP 发起请求。

```
http://${接入点}:8080/diamond-server/diamond
```

代码示例：

```
curl http://acm.aliyun.com:8080/diamond-server/diamond

# 返回
XX.XX.XX.144
```

## 通信协议

支持通过 HTTP 进行请求通信。

## 请求方法

支持 HTTP GET 或 POST 方法发送请求，GET 方式下请求参数需要包含在请求的 URL 中。

## 请求参数

每个请求都需要包含公共的鉴权、签名相关请求参数和相关操作所特有的请求参数。

## 公共参数

调用 ACM API 时都会用到的 Header 参数如下表所示。

|参数|类型|是否必需|描述|
|--|--|----|--|
|Spas-AccessKey|String|是|在 ACM 控制台上的命名空间详情对话框内可获取 AccessKey。|
|timeStamp|String|是|以毫秒为单位的请求时间。|
|Spas-Signature|String|是|使用 SecretKey 对“Tenant+TimeStamp”签名，注意加号+也是签名的一部分。（`SpasSigner.sign(Tenant+Group+TimeStamp, secretKey)`），签名算法为 HmacSHA1。TimeStamp 签名的作用是防止重放攻击。该签名有效期为 60 秒。|
|Spas-SecurityToken|String|否|SecurityToken 需从 STS 临时凭证中获取。STS 临时凭证需从实例元数据 URL 中获取。详情请参考： -   [借助于实例 RAM 角色访问其他云产品](https://www.alibabacloud.com/help/doc-detail/54579.htm)
-   [通过 ECS 实例 RAM 角色访问 ACM](https://www.alibabacloud.com/help/doc-detail/72013.htm) |
|longPullingTimeout|String|是|长轮询等待 30 秒，此处填写 30000。|

## 字符编码

请求及返回结果都使用 GBK 字符集进行编码。

## 签名机制

ACM 服务会对每个访问的请求进行身份验证，使用 HTTP 需要在请求中包含签名（Signature）信息。ACM 通过使用 AccessKey 和 SecretKey 进行对称加密的方法来验证请求的发送者身份。

AccessKey 和 SecretKey 由 ACM 颁发给访问者。其中 AccessKey 用于标识访问者的身份，SecretKey 是用于加密签名字符串和服务器端验证签名字符串的密钥，出于安全考虑，请务必严格保密。

## 签名算法

签名采用 HmacSHA1 算法。

-   Java 签名算法参考

    ```
    public static void main(String[] args) throws Exception {
        String tenant= "tenant";
        String group = "group";
        String timeStamp = String.valueOf(System.currentTimeMillis());
        String abc = HmacSHA1Encrypt(tenant+ "+" + group + "+" + timeStamp , "1234");
        System.out.println(abc);
    }
    public static String HmacSHA1Encrypt(String encryptText, String encryptKey) throws Exception {
        byte[] data = encryptKey.getBytes("UTF-8");
        // 根据给定的字节数组构造一个密钥，第二参数指定一个密钥算法的名称
        SecretKey secretKey = new SecretKeySpec(data, "HmacSHA1");
        // 生成一个指定 Mac 算法 的 Mac 对象
        Mac mac = Mac.getInstance("HmacSHA1");
        // 用给定密钥初始化 Mac 对象
        mac.init(secretKey);
        byte[] text = encryptText.getBytes("UTF-8");
        byte[] textFinal = mac.doFinal(text);
        // 完成 Mac 操作, base64编码，将byte数组转换为字符串
        return new String(Base64.encodeBase64(textFinal));
    }
    ```

-   Shell 签名算法

    ```
    ## config sign
    timestamp=`echo $[$(date +%s%N)/1000000]`
    signStr=$namespace+$group+$timestamp
    signContent=`echo -n $signStr | openssl dgst -hmac $sk -sha1 -binary | base64`
    echo $signContent
    ```


## 签名处理步骤

1.  使用请求参数构造规范的请求字符串（QueryParam）。
2.  使用上一步构造的规范字符串，按照以下规则构造用于计算签名的字符串。

    ```
    Signature=HMAC-SHA1(QueryParam)
    ```

    **说明：** 对于不同的请求，QueryParam 会不同。

3.  按照 RFC2104 的定义，使用上一步构造的用于签名的字符串来计算签名 HMAC 值。

    **说明：** 计算签名时使用的 Key 就是您持有的 AccessKeySecret（ASCII:38），使用的哈希算法是 SHA1。

4.  按照 Base64 编码规则将上一步算出的 HMAC 值编码成字符串，即得到签名值（Signature）。
5.  将上一步得到的签名值作为 Signature 参数添加到请求参数中，即完成对请求的签名处理。

## 示例代码

以下示例代码展示了如何以 Shell 构造 ACM 请求。

```
#!/bin/bash
## config param
dataId="com.alibaba.nacos.example.properties"
group="DEFAULT_GROUP"
namespace="04754ad1-4f67-4d67-b2bf-1f73a04a****"
accessKey="8c5cbb849ae04682ad9f455a96aa****"
secretKey="lwO5T7vfPJu27FclPa+/CyIG****"
endpoint="acm.aliyun.com"
## config param end
## get serverIp from address server
serverIp=`curl $endpoint:8080/diamond-server/diamond -s | awk '{a[NR]=$0}END{srand();i=int(rand()*NR+1);print a[i]}'`
## config sign
timestamp=`echo $[$(date +%s%N)/1000000]`
signStr=$namespace+$group+$timestamp
signContent=`echo -n $signStr | openssl dgst -hmac $secretKey -sha1 -binary | base64`
## request to get a config
curl -H "Spas-AccessKey:"$accessKey -H "timeStamp:"$timestamp -H "Spas-Signature:"$signContent "http://"$serverIp":8080/diamond-server/config.co?dataId="$dataId"&group="$group"&tenant="$namespace -v
```

## 限流机制

ACM 对访问频率采取限制，主要规则如下：

-   每个 IP 长连接数最多为 30 个。
-   每个 IP 每秒修改同一个配置不能超过 5 次。
-   每个 IP 每秒获取同一个配置不能超过 10 次。

