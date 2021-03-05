# ACM C++ SDK

本文介绍 ACM C++ SDK 的使用方式。ACM C++ SDK 只支持 Linux 平台。

## 安装 ACM C++ SDK

1.  下载 SDK 依赖包：[ACM C++ SDK](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/63523/cn_zh/1520758818047/acm-cpp-sdk.zip)
2.  下载完成后进行解压，会有如下目录结构：

    -   example/
    -   include/
    -   lib/
    上面的目录和文件的作用如下：

    -   example：acm.cpp 用于演示 SDK 使用。Makefile 用于 example 的编译和管理。
    -   include：您自己编写的程序需要 include 的头文件。
    -   lib：分别是 64 的静态库和动态库。
3.  设置环境变量 LD\_LIBRARY\_PATH，指定 ACM 动态库搜索路径，即安装的路径。

    ```
    export LD_LIBRARY_PATH=/usr/lib64:/usr/lib:/lib:/lib64:../lib
    ```

4.  进入 example 目录，修改 acm.cpp 文件的起始参数和配置的 dataId、group。执行 make 命令编译。执行示例代码如下：

    ```
    cd example   //进入example目录
    vim acm.cpp  //修改acm.cpp文件
    make         //编译示例代码
    ./acm        //执行示例代码
    ```


## 示例代码

在您的工程中运行以下代码进行配置管理。

```
#include "ACM.h"
using namespace std;
using namespace acm;
//定义监听器
class MyListener : public ManagerListener
{
public:
    MyListener(const std::string& data_id, const std::string& group):data_id_(data_id),group_(group){}
    virtual ~MyListener()
    {}
    virtual void getExecutor()
    {
        printf("data_id:%s group:%s getExecutor\n",data_id_.c_str(), group_.c_str());
    }
    //配置变更时回调
    virtual void receiveConfigInfo( std::string &configInfo)
    {
        printf("data_id:%s group:%s configInfo:\n%s\n", data_id_.c_str(), group_.c_str(), configInfo.c_str());
        config_ = configInfo;
    }
private:
    std::string data_id_;
    std::string group_;
    std::string config_;
};
int main() {
    ACM::init("acm.aliyun.com",  // endpoint: ACM 控制台查看
              "***************", // namespace: ACM 控制台查看 
              "***************", // accessKey: ACM 控制台查看
              "***************");  // secretKey: ACM 控制台查看
    //监听配置的dataId和group
    std::string dataId = "${dataId}";
    std::string group = "${group}";
    std::string content;
    //主动拉取配置
    ACM::getConfig(dataId, group, 5000, content);
    printf("get ok config %s\n", content.c_str());
    MyListener* listener = new MyListener(dataId, group);
    //监听配置变更
    ACM::addListener(dataId, group, listener);
    printf("add listener ok %s %s\n", dataId.c_str(), group.c_str());
    do {
        printf("input q to quit\n");
    } while (getchar() != 'q');
    return 0;
}
```

## 接口说明

-   初始化设置接口

    设置 endpoint，nameSpace，accessKey，secretKey。

    ```
    static void init(const char* endpoint, 
                     const char* nameSpace, 
                     char* accessKey, 
                     char* secretKey);
    ```

    各参数说明如下：

    |参数名|参数类型|描述|
    |---|----|--|
    |endpoint|const char\*|ACM 域名，控制台获得|
    |nameSpace|const char\*|命名空间，控制台获得|
    |accessKey|char\*|accessKey，控制台获得|
    |secretKey|char\*|secretKey，控制台获得|

-   获取配置接口

    用于服务启动的时候从 ACM 获取配置。

    ```
    static bool getConfig(const std::string &dataId,
                          const std::string &group,
                          int timeoutMs,
                          std::string &content);
    ```

    参数说明见下表：

    |参数名|参数类型|描述|
    |---|----|--|
    |dataId|const string|配置 ID，采用类似 package.class（如com.taobao.tc.refund.log.level）的命名规则保证全局唯一性，class 部分建议是配置的业务含义。全部字符小写。只允许英文字符和 4 种特殊字符（”.”、”:”、”-“、”\_”），不超过 256 字节。|
    |group|const string|配置分组，建议填写**产品名：模块名**（如 ACM:Test）保证唯一性，只允许英文字符和 4 种特殊字符（”.”、”:”、”-“、”\_”），不超过 128 字节。|
    |timeoutMs|int|超时时间|
    |content|string|返回的配置内容|

-   监听配置接口

    如果希望 ACM 推送配置变更，可以使用 ACM 动态监听配置接口来实现。

    ```
    static  void addListener(const std::string &dataId,
                             const std::string &group,
                             ManagerListener* listener);
    ```

    参数说明见下表：

    |参数名|参数类型|描述|
    |---|----|--|
    |dataId|const string|dataId|
    |group|const string|group|
    |listener|ManagerListener|监听器，配置变更会执行监听器的回调函数。|

-   取消配置监听接口

    用于服务启动的时候取消配置监听。

    ```
    static  void removeListener(const std::string &dataId,
                                const std::string &group,
                                ManagerListener* listener);
    ```

    参数说明见下表：

    |参数名|参数类型|描述|
    |---|----|--|
    |dataId|const string|dataId|
    |group|const string|group|
    |listener|ManagerListener|待取消的监听器|


