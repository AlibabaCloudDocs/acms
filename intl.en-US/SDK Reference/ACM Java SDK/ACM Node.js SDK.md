# ACM Node.js SDK

## Install the ACM Client for Node.js

ACM Client for Node.js makes it possible to help front-end developers release the front and back ends independently, and separate development from operations, which improves the development efficiency dramatically.

Enter the following command to install the client:

```
npm i acm-client --save
```

## Sample code

Run the following code in your project for configuration management.

```
const ACMClient = require('acm-client');
const co = require('co');
const acm = new ACMClient({
  endpoint: 'acm.aliyun.com', // Can be found in the ACM console
  namespace: '***************', // Can be found in the ACM console
  accessKey: '***************', // Can be found in the ACM console
  secretKey: '***************', // Can be found in the ACM console
  requestTimeout: 6000, // Length of request time-out, 6s by default.
});
// Actively pull the configuration.
co(function*() {
  const content= yield acm.getConfig('test', 'DEFAULT_GROUP');
  console.log('getConfig = ',content);
});
// Listening for data updating
acm.subscribe({
  dataId: 'test',
  group: 'DEFAULT_GROUP',
}, content => {
  console.log(content);
});
```

## Error events handling

```
acm.on('error', function (err) {
  // Logs can be recorded here centrally
  // If error events are not listened for, all exceptions get printed to stderr.
});
```

## API description

-   API for getting configurations

    Used to get the configuration from ACM when the service starts.

    ```
    function* getConfig(dataId, group)
    ```

-   Request parameters

    |Parameter name|Parameter type|Description|
    |--------------|--------------|-----------|
    |dataId|string|Configuration ID. Use a naming rule like package.class \(for example, com.taobao.tc.refund.log.level\) to ensure global uniqueness. It is recommended to indicate business meaning of the configuration in the “class” section. All characters must be in lowercase. Only English characters and four special characters \(".", ":", "-", and "\_"\) are allowed. It must not exceed 256 bytes.|
    |group|string|Configuration group. We recommend that you use **product name: module name** \(for example ACM: Test\) to guarantee the uniqueness. Only English characters and four special characters \(".", ":", "-", and "\_"\) are allowed. It must not exceed 128 bytes.|

-   Return values

    |Parameter type|Description|
    |--------------|-----------|
    |string|Configuration value|

-   API for configuration listening

    If you want ACM to push configuration changes, you can use the ACM dynamic configuration listening interface.

    ```
    function subscribe(info, listener)
    ```

-   Request parameters

    |Parameter name|Parameter type|Description|
    |--------------|--------------|-----------|
    |info|Object|Info.dataId: Configuration ID. info.group: Configuration group|
    |listener|Function|Listener. Configuration changes go into the callback function of the listener.|

-   API for canceling configuration listening

    Cancels configuration listening when the service starts.

    ```
    function unSubscribe(info, [listener])
    ```

-   Request parameters

    |Parameter name|Parameter type|Description|
    |--------------|--------------|-----------|
    |info|Object|info.dataId: Configuration ID. Info.group: Configuration groupp\||
    |listener|Function|Callback function. \(Optional, all listener functions are removed if this parameter is not passed in.\)|


## Node.js project link

[https://www.npmjs.com/package/acm-client](https://www.npmjs.com/package/acm-client)

## Feedback

-   [@hustxiaoc](https://github.com/hustxiaoc)

