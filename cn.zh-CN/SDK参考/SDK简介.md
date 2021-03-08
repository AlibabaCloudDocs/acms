---
keyword: [SDK, 应用层, 配置, 监听]
---

# SDK简介

SDK是ACM提供给用户在应用运行时获取、发布、监听、删除配置的手段。同时，我们也开放了SDK底层使用的API协议，如SDK不能满足您的使用场景，您也可以基于API协议来实现应用运行时配置操作和监听的功能。

**说明：** 此SDK用于在应用中操作和监听配置，如您需要在管控节点对应用配置进行管理，可考虑使用[获取OpenAPI SDK](/cn.zh-CN/OpenAPI参考/获取OpenAPI SDK.md)。

## ACM SDK支持的类型

ACM SDK目前支持以下类型：

-   [Nacos Java SDK](/cn.zh-CN/SDK参考/Nacos SDK/Nacos Client.md)：支持ACM配置监听和变更的Java原生SDK。
-   [spring-cloud-starter-alibaba-nacos-config](/cn.zh-CN/SDK参考/Nacos SDK/Nacos Spring Cloud.md)：支持Spring Cloud Config接口规范的Java SDK。
-   [Nacos Go SDK](/cn.zh-CN/SDK参考/Nacos Go SDK/Nacos Go SDK 概述.md)：支持ACM配置监听和变更的Golang原生SDK。
-   [Nacos Node.js SDK](https://github.com/nacos-group/nacos-sdk-nodejs)：支持ACM配置监听和变更的Node.js原生SDK。
-   [Nacos C++ SDK](https://github.com/nacos-group/nacos-sdk-cpp)：支持ACM配置监听和变更的C++原生SDK。
-   [Nacos Python SDK](https://github.com/nacos-group/nacos-sdk-python)：支持ACM配置监听和变更的Python原生SDK。
-   [Nacos C\# SDK](https://github.com/nacos-group/nacos-sdk-csharp)：支持ACM配置监听和变更的C\#原生SDK。

以下为已经废弃的SDK：

-   ACM Java Native SDK（已废弃）：支持ACM配置监听和变更的Java原生SDK。
-   Spring Cloud ACM（已废弃）：支持Spring Cloud Config接口规范的Java SDK。
-   ACM Node.js SDK（已废弃）：支持ACM配置监听和变更的Node.js原生SDK。
-   ACM C++ SDK（已废弃）：支持ACM配置监听和变更的C++原生SDK。
-   ACM Python（已废弃）：支持ACM配置监听和变更的Python原生SDK。
-   ACM PHP（已废弃）：支持ACM配置监听的PHP原生SDK。

## ACM SDK功能特性

ACM SDK 的功能特性如下：

|功能|Java Native|Java Spring Cloud|Python|Node.JS|C++|PHP|Nacos SDK|
|--|-----------|-----------------|------|-------|---|---|---------|
|获取特定配置|支持|支持|支持|支持|支持|支持|支持|
|监听特定配置|支持|支持|支持|支持|支持|不支持|支持|
|写入配置|支持|不支持|支持|不支持|不支持|支持|支持|
|支持固定租户下枚举配置|支持|不支持|支持|不支持|不支持|不支持|不支持|
|支持单一IP方式进行服务端连接|支持|不支持|支持|不支持|不支持|不支持|支持|
|支持多IP LB方式进行服务端连接|支持|支持|支持|支持|支持|支持|支持|
|支持HmacSHA1算法方式用户认证|支持|支持|支持|支持|支持|支持|支持|
|支持本地缓存备份|支持|支持|支持|支持|支持|不支持|支持|
|[支持 ECS 实例 RAM 角色鉴权](/cn.zh-CN/访问控制/通过ECS实例RAM角色访问ACM.md)|支持|支持|支持|不支持|不支持|不支持|支持|
|开源地址|计划中|计划中|[acm-sdk-python](https://github.com/alibaba/acm-sdk-python)|计划中|计划中|[acm-sdk-php](https://github.com/alibaba/acm-sdk-php)|[Nacos](https://github.com/alibaba/nacos)|

-   多IP LB方式是指ACM SDK基于地址服务器返回的多ACM服务端IP地址的LoadBalance功能，以提高性能和实现高可用。
-   本地缓存备份功能可以让ACM SDK在没有服务端连接情况下，通过读取上次获取配置后保存的本地缓存备份文件，来避免客户端应用宕机。
-   `spring-cloud-starter-acm`后续将不再继续维护，推荐使用Nacos的 [spring-cloud-starter-alibaba-nacos-config](/cn.zh-CN/SDK参考/Nacos SDK/Nacos Spring Cloud.md)。

## 地域和接入点列表

|地域|地址服务器域名|
|--|-------|
|公网|acm.aliyun.com|
|华东 1（杭州）|addr-hz-internal.edas.aliyun.com|
|华北 1（青岛）|addr-qd-internal.edas.aliyun.com|
|华东 2（上海）|addr-sh-internal.edas.aliyun.com|
|华北 2（北京）|addr-bj-internal.edas.aliyun.com|
|华南 1（深圳）|addr-sz-internal.edas.aliyun.com|
|中国（香港）|addr-hk-internal.edas.aliyuncs.com|
|新加坡（新加坡）|addr-singapore-internal.edas.aliyun.com|
|澳大利亚（悉尼）|addr-ap-southeast-2-internal.edas.aliyun.com|
|美国（硅谷）|addr-us-west-1-internal.acm.aliyun.com|
|美国（弗吉尼亚）|addr-us-east-1-internal.acm.aliyun.com|
|华东 2（上海）金融云|addr-cn-shanghai-finance-1-internal.edas.aliyun.com|

