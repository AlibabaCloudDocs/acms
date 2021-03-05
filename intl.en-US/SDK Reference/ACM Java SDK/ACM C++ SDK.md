# ACM C++ SDK

This topic explains how to use ACM C++ SDK. ACM C++ SDK only supports Linux platform.

## Install ACM C++ SDK

1.  Download SDK dependency package: [ACM C++ SDK](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/63523/cn_zh/1520758818047/acm-cpp-sdk.zip)
2.  Extract the downloaded package to create the following directory structure:

    -   example/
    -   include/
    -   lib/
    The preceding directories and files serve the following purposes:

    -   example: acm.cpp demonstrates how to use SDK.  Makefile compiles and manages the example directory.
    -   include: the header files to be included in your own program.
    -   lib: The directory contains the 64-bit static library and dynamic library.
3.  The environment variable LD\_LIBRARY\_PATH specifies the ACM dynamic library search path, which is also the path for installation.

    ```
    export LD_LIBRARY_PATH=/usr/lib64:/usr/lib:/lib:/lib64:../lib
    ```

4.  Go to the example directory and modify the start parameter of the acm.cpp file and the dataId and group parameters. Run the make command for compiling.  Run the following sample code:

    ```
    cd example //Enter the example directory
    vim acm.cpp //Modify the acm.cpp file
    make //Compile the sample code
    ./acm //Run the sample code
    ```


## Sample code

Run the following code in your project for configuration management.

```
#include "ACM.h"
using namespace std;
using namespace acm;
//Define the listener.
class MyListener : public ManagerListener
{
public:
    MyListener(const std::string&amp; data_id, const std::string&amp; group):data_id_(data_id),group_(group){}
    virtual ~MyListener()
    {}
    virtual void getExecutor()
    {
        printf("data_id:%s group:%s getExecutor\n",data_id_.c_str(), group_.c_str());
    }
    //Callback during configuration modification
    virtual void receiveConfigInfo( std::string &amp;configInfo)
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
    ACM::init("acm.aliyun.com", // endpoint: Can be found in the ACM console.
              "***************", // namespace: Can be found in the ACM console. 
              "***************", // accessKey: Can be found in the ACM console.
              "***************"); // secretKey: Can be found in the ACM console.
    //The dataId and group of the configuration that is listened for
    std::string dataId = "${dataId}";
    std::string group = "${group}";
    std::string content;
    //Actively pull the configuration
    ACM::getConfig(dataId, group, 5000, content);
    printf("get ok config %s\n", content.c_str());
    MyListener* listener = new MyListener(dataId, group);
    //Listen for configuration changes
    ACM::addListener(dataId, group, listener);
    printf("add listener ok %s %s\n", dataId.c_str(), group.c_str());
    do {
        printf("input q to quit\n");
    } while (getchar() ! = 'q');
    return 0;
}
```

## Interface description

-   Interface for initialization

    Set endpoint, nameSpace, accessKey, and secretKey.

    ```
    static void init(const char* endpoint, 
                     const char* nameSpace, 
                     char* accessKey, 
                     char* secretKey);
    ```

    Parameters are described as follows:

    |Parameter name|Parameter type|Description|
    |--------------|--------------|-----------|
    |endpoint|const char\*|ACM domain name, which is found in the console|
    |nameSpace|const char\*|Namespace, which is found in the console|
    |accessKey|char\*|accessKey, which is found in the console|
    |secretKey|char\*|secretKey, which is found in the console|

-   Interface for getting configurations

    Obtains configurations from ACM when the service starts.

    ```
    static bool getConfig(const std::string &amp;dataId,
                          const std::string &amp;group,
                          int timeoutMs,
                          std::string &amp;content);
    ```

    Parameters are described as follows:

    |Parameter name|Parameter type|Description|
    |--------------|--------------|-----------|
    |dataId|const string|Configuration ID. Use a naming rule like package.class \(for example, com.taobao.tc.refund.log.level\) to ensure global uniqueness. It is recommended to indicate business meaning of the configuration in the “class” section. All characters must be in lower case. Only English characters and four special characters \(".", ":", "-", and "\_"\) are allowed. It must not exceed 256 bytes.|
    |group|const string|Configuration group. We recommend that you use **product name: module name** \(for example ACM: Test\) to guarantee the uniqueness. Only English characters and four special characters \(".", ":", "-", and "\_"\) are allowed. It must not exceed 128 bytes.|
    |timeoutMs|int|Time-out period|
    |content|string| Returned configuration content|

-   Listening to configuration interface

    If you want ACM to push configuration changes, you can use the ACM dynamic configuration listening interface.

    ```
    static void addListener(const std::string &amp;dataId,
                             const std::string &amp;group,
                             ManagerListener* listener);
    ```

    Parameters are described as follows:

    |Parameter name|Parameter type|Description|
    |--------------|--------------|-----------|
    |dataId|const string|dataId|
    |group|const string|group|
    |listener|ManagerListener|Listener. The listener callback function will be run when configuration is modified.|

-   Cancel configuration listening interface

    Cancels configuration listening when the service starts.

    ```
    static void removeListener(const std::string &amp;dataId,
                                const std::string &amp;group,
                                ManagerListener* listener);
    ```

    Parameters are described as follows:

    |Parameter name|Parameter type|Description|
    |--------------|--------------|-----------|
    |dataId|const string|dataId|
    |group|const string|group|
    |listener|Manager listener|Listener to be canceled|


