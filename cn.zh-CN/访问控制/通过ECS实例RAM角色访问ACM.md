---
keyword: [RAM角色, 安全性, 权限]
---

# 通过ECS实例RAM角色访问ACM

如果借助ECS实例RAM角色，则无需配置AccessKey（AK）即可访问ACM，从而提高安全性。

## 背景信息

以往，如果部署在ECS实例中的应用程序需要访问ACM，必须将AccessKey以配置文件或其他形式保存在ECS实例中，这在一定程度上增加了AccessKey管理的复杂性，并且降低了AccessKey的保密性。创建AccessKey的具体操作，请参见[创建AccessKey]()。

现在，借助[ECS实例RAM角色](/cn.zh-CN/安全/实例RAM角色/概述.md)，您可以将RAM角色和ECS实例关联起来，然后将RAM角色名称告知ACM SDK（1.0.8及以上版本），此后无需配置AccessKey即可访问ACM。另外，借助[RAM（访问控制）](/cn.zh-CN/产品简介/什么是访问控制.md)，您可以通过角色和授权策略实现不同实例对ACM具有不同访问权限的目的。例如，如果配置只读策略，关联了该角色的ECS就只能读取ACM的配置，而无法新增或修改ACM配置。

## 前提条件

您已成功创建ECS实例，且ECS实例的网络环境为专有网络（VPC）。

## 步骤一：创建RAM角色并配置授权策略

1.  云账号登录[RAM控制台](https://ram.console.aliyun.com/)。

2.  在左侧导航栏，单击**RAM角色管理**。

3.  单击**创建RAM角色**，选择可信实体类型为**阿里云服务**，单击**下一步**。

4.  选择**角色类型**，输入**角色名称**和**备注**，选择受信服务为**云服务器**，然后单击**完成**。

5.  在**RAM角色名称**列，找到刚创建的RAM角色。

6.  在**操作**列单击**添加权限**。

7.  在添加权限对话框中，通过关键词搜索授权策略`AliyunACMFullAccess`，并单击该授权策略将其添加至右侧的**已选授**列表，然后单击**确定**。

    **说明：** 如果需要用到加解密配置功能，则还要添加`AliyunKMSCryptoAdminAccess`授权策略。

    此时，该角色已具备ACM的所有操作权限。


## 步骤二：为ECS实例授予该RAM角色

1.  登录[ECS控制台](https://ecs.console.aliyun.com/#/home)，单击左侧导航栏的**实例**。
2.  单击实例列表中目标ECS实例**操作**栏的**更多** \> **实例设置** \> **授予/收回RAM角色**。

    ![实例配置](https://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/pg_ECS_instance.png)

3.  在授予/收回RAM角色对话框中选择**RAM角色**为[步骤一](#section_q5l_55j_pgb)创建的RAM角色。

    **说明：** 若您还未创建RAM角色，请单击**创建RAM角色**进行创建。

    ![RAM实例角色授权](https://aliware-images.oss-cn-hangzhou.aliyuncs.com/acms/pg_grant_ECS_permission.png)


## 步骤三：将RAM角色名称告知ACM SDK并访问配置

将RAM角色名称告知ACM SDK（版本1.0.8及以上）方法有两种：通过JVM参数设置和通过代码传参设置。

**说明：** JVM参数设置方式优先级高于代码传参方式。

-   通过JVM参数设置：

    -   格式：`-Dram.role.name=$ramRoleName`

    -   示例：`-Dram.role.name=ECS-RAM`

-   通过代码传参设置：

    ```
    import java.util.Properties;
    import com.alibaba.edas.acm.ConfigService;
    import com.alibaba.edas.acm.exception.ConfigException;
    // 示例代码，仅用于示例测试public class ACMTest {
        public static void main(String[] args) {
            try {
                Properties properties = new Properties();
                // endpoint可以从ACM控制台“命名空间详情”或“示例代码”中获取properties.put("endpoint", "$endpoint");
                // namespace可以从ACM控制台“命名空间详情”或“示例代码”中获取properties.put("namespace", "$namespace");
                // 刚刚新建并绑定到ECS实例的RAM角色名称，如“ECS-RAM”properties.put("ramRoleName", "$ramRoleName");
                ConfigService.init(properties);
                // 主动获取配置String content = ConfigService.getConfig("${dataId}", "${group}", 6000);
                System.out.println(content);
            } catch (ConfigException e) {
                e.printStackTrace();
            }
        }
    }
    ```


## 更多信息

-   [Access Key（AK）]()
-   [RAM（访问控制）](/cn.zh-CN/产品简介/什么是访问控制.md)
-   [创建可信实体为阿里云服务的RAM角色](/cn.zh-CN/角色管理/创建RAM角色/创建可信实体为阿里云服务的RAM角色.md)
-   [使用实例RAM角色访问其他云产品](/cn.zh-CN/最佳实践/使用实例RAM角色访问其他云产品.md)
-   [ACM Java Native SDK 概述](/cn.zh-CN/SDK参考/ACM Java SDK/ACM Java Native SDK/ACM Java Native SDK 概述.md)

