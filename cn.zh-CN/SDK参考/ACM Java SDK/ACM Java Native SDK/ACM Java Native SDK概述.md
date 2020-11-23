# ACM Java Native SDK概述

您可以使用ACM Java Native SDK来获取、监听、发布和删除配置。

## 添加依赖

在pom.xml文件中添加以下依赖，即可开始使用ACM Java Native SDK。

```
<dependency>
    <groupId>com.alibaba.edas.acm</groupId>
    <artifactId>acm-sdk</artifactId>
    <version>1.0.9</version>
</dependency>
<!--  如果已有日志实现，则可去除以下依赖  -->
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.1.7</version>
</dependency>
```

**说明：** 如需使用以KMS AES-128方式加密的配置，则依赖的acm-sdk版本不可低于1.0.9。

## 示例代码

添加依赖后，即可在程序中使用ACM Java Native SDK提供的接口。

**说明：** 请将代码中的 $regionId、$endpoint、$namespace、$accessKey、$secretKey 分别替换为 ACM 控制台上命名空间详情对话框内的**地域 ID**、**End Point**、**命名空间 ID**、**AccessKey**、**SecretKey**。

```
import java.util.Properties;
import com.alibaba.edas.acm.ConfigService;
import com.alibaba.edas.acm.exception.ConfigException;
import com.alibaba.edas.acm.listener.ConfigChangeListener;
import com.alibaba.edas.acm.listener.PropertiesListener;
// 示例代码，仅用于示例测试public class ACMTest {
    // 属性/开关private static String config = "DefaultValue";
    private static Properties acmProperties = new Properties();
    public static void main(String[] args) {
        try {
            // 从控制台命名空间管理中拷贝对应值Properties properties = new Properties();
            properties.put("endpoint", "$endpoint");
            properties.put("namespace", "$namespace");
            // 通过ECS实例RAM角色访问ACM
            // properties.put("ramRoleName", "$ramRoleName");
            properties.put("accessKey", "$accessKey");
            properties.put("secretKey", "$secretKey");
            // 如果是加密配置，则添加下面两行进行自动解密
            //properties.put("openKMSFilter", true);
            //properties.put("regionId", "$regionId");
            ConfigService.init(properties);
            // 主动获取配置String content = ConfigService.getConfig("${dataId}", "${group}", 6000);
            System.out.println(content);
            // 初始化的时候，给配置添加监听，配置变更会回调通知ConfigService.addListener("${dataId}", "${group}", new ConfigChangeListener() {
                public void receiveConfigInfo(String configInfo) {
                    // 当配置更新后，通过该回调函数将最新值返回给用户。
                    // 注意回调函数中不要做阻塞操作，否则阻塞通知线程。config = configInfo;
                    System.out.println(configInfo);
                }
            });
            /**
             * 如果配置值的內容为properties格式（key=value），可使用下面监听器。以便一个配置管理多个配置项
             */
            /**
            ConfigService.addListener("${dataId}", "${group}", new PropertiesListener() {
                @Override
                public void innerReceive(Properties properties) {
                    // TODO Auto-generated method stub
                    acmProperties = properties;
                    System.out.println(properties);
                }
            });
            **/
        } catch (ConfigException e) {
            e.printStackTrace();
        }
        // 测试让主线程不退出，因为订阅配置是守护线程，主线程退出守护线程就会退出。 正式代码中无需下面代码while (true) {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
    // 通过get接口把配置值暴露出去使用public static String getConfig() {
        return config;
    }
    // 通过get接口把配置值暴露出去使用public static Object getPorpertiesValue(String key) {
        if (acmProperties != null) {
            return acmProperties.get(key);
        }
        return null;
    }
}
```

## 传参方式

为了帮助您快速入门，以上示例代码中采用了以代码初始化参数的方式。但是，实际生产中可能有不同环境，例如不同的账号、地域或命名空间，而参数因环境而异，因此需要通过变量传入。为了方便配置入参和降低配置成本，ACM提供了多种传参方式。

**说明：** 在EDAS环境下发布需要注意，EDAS没有公网环境。

|初始化参数|传参方法|
|-----|----|
|endpoint|优先级由高到低：1.  JVM参数：`-Daddress.server.domain=xxx`
2.  环境变量：`address_server_domain=xxx`
3.  代码设置：如以上[示例代码](#sec_sample_code) |
|namespace|优先级由高到低：1.  JVM参数：`-Dtenant.id=xxx`
2.  代码设置：如以上[示例代码](#sec_sample_code) |
|ramRoleName|优先级由高到低：1.  JVM参数：`-Dram.role.name=xxx`
2.  代码设置：如以上[示例代码](#sec_sample_code)

**说明：** ramRoleName的鉴权优先级高于accessKey/ secretKey。 |
|accessKey/secretKey|优先级由高到低：1.  文件方式：accessKey和secretKey以Properties格式（满足`public void java.io.Reader.Properties.load(Reader reader)`方法）存储在-Dspas.identity指定文件中。如果不指定，则默认取/home/admin/.spas\_key/<appName\>文件（<appName\>以-Dproject.name指定）
2.  环境变量：`spas_accessKey=xxx spas_secretKey=xxx`
3.  代码设置：如以上[示例代码](#sec_sample_code) |

## 更多信息

-   [获取配置](/cn.zh-CN/SDK参考/ACM Java SDK/ACM Java Native SDK/获取配置.md)
-   [监听配置](/cn.zh-CN/SDK参考/ACM Java SDK/ACM Java Native SDK/监听配置.md)
-   [发布配置](/cn.zh-CN/SDK参考/ACM Java SDK/ACM Java Native SDK/发布配置.md)
-   [删除配置](/cn.zh-CN/SDK参考/ACM Java SDK/ACM Java Native SDK/删除配置.md)
-   [通过ECS实例RAM角色访问ACM](/cn.zh-CN/访问控制/通过ECS实例RAM角色访问ACM.md)

