# ACM Node.js SDK

## 安装 ACM Node.js 版客户端

ACM Node.js 版客户端主要用于帮助前端开发解决前后端独立发布，开发与运营独立问题，大幅提升开发效率。

输入以下命令安装客户端：

```
npm i acm-client --save
```

## 示例代码

在您的工程中运行以下代码进行配置管理。

```
const { ACMClient } = require('acm-client');
const co = require('co');
const acm = new ACMClient({
  endpoint: 'acm.aliyun.com', // ACM 控制台查看
  namespace: '***************', // ACM 控制台查看
  accessKey: '***************', // ACM 控制台查看
  secretKey: '***************', // ACM 控制台查看
  requestTimeout: 6000, // 请求超时时间，默认 6s
});
// 主动拉取配置
co(function*() {
  const content= yield acm.getConfig('test', 'DEFAULT_GROUP');
  console.log('getConfig = ',content);
});
// 监听数据更新
acm.subscribe({
  dataId: 'test',
  group: 'DEFAULT_GROUP',
}, content => {
  console.log(content);
});
```

## Error Events 异常处理

```
acm.on('error', function (err) {
  // 可以在这里统一进行日志的记录
  // 如果不监听错误事件，所有的异常都将会打印到 stderr
});
```

## 接口说明

-   获取配置接口

    用于服务启动的时候从 ACM 获取配置。

    ```
    function* getConfig(dataId, group)
    ```

-   请求参数

    |参数名|参数类型|描述|
    |---|----|--|
    |dataId|string|配置 ID，采用类似 package.class（如com.taobao.tc.refund.log.level）的命名规则保证全局唯一性，class 部分建议是配置的业务含义。全部字符小写。只允许英文字符和 4 种特殊字符（”.”、”:”、”-“、”\_”），不超过 256 字节。|
    |group|string|配置分组，建议填写**产品名：模块名**（如 ACM:Test）保证唯一性，只允许英文字符和 4 种特殊字符（”.”、”:”、”-“、”\_”），不超过 128 字节。|

-   返回值

    |参数类型|描述|
    |----|--|
    |string|配置值|

-   监听配置接口

    如果希望 ACM 推送配置变更，可以使用 ACM 动态监听配置接口来实现。

    ```
    function subscribe(info, listener)
    ```

-   请求参数

    |参数名|参数类型|描述|
    |---|----|--|
    |info|Object|info.dataId：配置 ID。 info.group：配置分组|
    |listener|Function|监听器，配置变更进入监听器的回调函数。|

-   取消配置监听接口

    用于服务启动的时候取消配置监听。

    ```
    function unSubscribe(info, [listener])
    ```

-   请求参数

    |参数名|参数类型|描述|
    |---|----|--|
    |info|Object|info.dataId:配置 ID。 info.group:配置分组|
    |listener|Function|回调函数（可选，不传就移除所有监听函数）。|


## Node.js 项目链接

[https://www.npmjs.com/package/acm-client](https://www.npmjs.com/package/acm-client)

## 问题反馈

-   [@hustxiaoc](https://github.com/hustxiaoc)

