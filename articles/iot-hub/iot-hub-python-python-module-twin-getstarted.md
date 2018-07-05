---
title: Azure IoT 中心模块标识和模块孪生 (Python) 入门 | Microsoft Docs
description: 了解如何使用用于 Python 的 IoT SDK 创建模块标识和更新模块孪生。
author: chrissie926
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: 7ef4d00f34cdf35c670099baa6c3bc655d94afb4
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034305"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-python-back-end-and-python-device"></a>使用 Python 后端和 Python 设备创建 IoT 中心模块标识和模块孪生入门

> [!NOTE]
> [模块标识和模块孪生](iot-hub-devguide-module-twins.md)类似于 Azure IoT 中心设备标识和设备孪生，但提供更精细的粒度。 Azure IoT 中心设备标识和设备孪生允许后端应用程序配置设备并提供设备条件的可见性，而模块标识和模块孪生为设备的各个组件提供这些功能。 在支持多个组件的设备上（例如基于操作系统的设备或固件设备），它允许每个部件拥有独立的配置和条件。

在本教程结束时，会创建两个 Python 应用：

* **CreateIdentities**，用于创建设备标识、模块标识和相关的安全密钥，以连接设备和模块客户端。
* **UpdateModuleTwinReportedProperties**，用于将更新的模块孪生报告属性发送到 IoT 中心。

> [!NOTE]
> 有关 Azure IoT SDK 的信息（可以使用这些 SDK 构建可在设备和解决方案后端上运行的应用程序），请参阅 [Azure IoT SDK][lnk-hub-sdks]。

要完成本教程，需要以下各项：

* 有效的 Azure 帐户。 （如果没有帐户，只需花费几分钟就能创建一个[免费帐户][lnk-free-trial]。）
* IoT 中心。
* 安装最新 [Python SDK](https://github.com/Azure/azure-iot-sdk-python)。


现已创建 IoT 中心，因此已获得完成本教程的其余部分所需的主机名和 IoT 中心连接字符串。

<a id="DeviceIdentity_csharp"></a>
## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>在 IoT 中心中创建设备标识和模块标识

本部分将创建一个 Python 应用，用于在 IoT 中心的标识注册表中创建设备标识和模块标识。 设备或模块无法连接到 IoT 中心，除非它在标识注册表中具有条目。 有关详细信息，请参阅 [IoT 中心开发人员指南][lnk-devguide-identity]中的“标识注册表”部分。 运行此控制台应用时，它会为设备和模块生成唯一的 ID 和密钥。 设备和模块在向 IoT 中心发送设备到云的消息时，使用这些值来标识自身。 ID 区分大小写。

将以下代码添加到 Python 文件：

```Python
import sys
import iothub_service_client
from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod, IoTHubError

CONNECTION_STRING = "YourConnString"
DEVICE_ID = "myFirstDevice"
MODULE_ID = "myFirstModule"

try:
    # RegistryManager
    iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

    # CreateDevice
    primary_key = ""
    secondary_key = ""
    auth_method = IoTHubRegistryManagerAuthMethod.SHARED_PRIVATE_KEY
    new_device = iothub_registry_manager.create_device(DEVICE_ID, primary_key, secondary_key, auth_method)
    print("new_device <" + DEVICE_ID + "> has primary key = " + new_device.primaryKey)

    # CreateModule
    new_module = iothub_registry_manager.create_module(DEVICE_ID, primary_key, secondary_key, MODULE_ID, auth_method)
    print("device/new_module <" + DEVICE_ID + "/" + MODULE_ID + "> has primary key = " + new_module.primaryKey)

except IoTHubError as iothub_error:
    print ( "Unexpected error {0}".format(iothub_error) )
except KeyboardInterrupt:
    print ( "IoTHubRegistryManager sample stopped" )
```

此应用在设备“myFirstDevice”下创建 ID 为“myFirstDevice”的设备标识，以及 ID 为“myFirstModule”的模块标识。 （如果该模块 ID 已在标识注册表中，代码就只检索现有的模块信息。）然后，应用程序会显示该标识的主密钥。 在模拟模块应用中使用此密钥连接到 IoT 中心。

> [!NOTE]
> IoT 中心标识注册表只存储设备和模块标识，以启用对 IoT 中心的安全访问。 标识注册表存储用作安全凭据的设备 ID 和密钥。 标识注册表还为每个设备存储启用/禁用标志，该标志可以用于禁用对该设备的访问。 如果应用程序需要存储其他特定于设备的元数据，则应使用特定于应用程序的存储。 没有针对模块标识的“已启用/已禁用”标记。 有关详细信息，请参阅 [IoT 中心开发人员指南][lnk-devguide-identity]。


<a id="D2C_csharp"></a>
## <a name="update-the-module-twin-using-python-device-sdk"></a>使用 Python 设备 SDK 更新模块孪生

本节将在更新模块孪生报告属性的模拟设备上创建 Python 应用。

1. **获取模块连接字符串** -- 现在，如果登录到 [Azure 门户][lnk-portal]。 导航到 IoT 中心并单击 IoT 设备。 查找并打开 myFirstDevice，可以看到 myFirstModule 已成功创建。 复制模块连接字符串。 下一步将需要它。

    ![Azure 门户模块详细信息][15]

2. **创建 UpdateModuleTwinReportedProperties 应用** 在“Program.cs”文件顶部添加以下 `using` 语句：

    ```Python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod, IoTHubDeviceTwin, IoTHubError

    CONNECTION_STRING = "FILL IN CONNECTION STRING"
    DEVICE_ID = "MyFirstDevice"
    MODULE_ID = "MyFirstModule"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"telemetryInterval\":122}}}"

    try:
        iothub_twin = IoTHubDeviceTwin(CONNECTION_STRING)

        twin_info = iothub_twin.get_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Twin before update    :" )
        print ( "{0}".format(twin_info) )

        twin_info_updated = iothub_twin.update_twin(DEVICE_ID, MODULE_ID, UPDATE_JSON)
        print ( "" )
        print ( "Twin after update     :" )
        print ( "{0}".format(twin_info_updated) )

    except IoTHubError as iothub_error:
        print ( "Unexpected error {0}".format(iothub_error) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

此代码示例演示如何检索模块孪生和借助 AMQP 协议更新报告属性。 

## <a name="get-updates-on-the-device-side"></a>在设备端获取更新
除了上述代码，还可添加以下代码块以在设备上获取孪生更新消息。

```Python
import random
import time
import sys
import iothub_client
from iothub_client import IoTHubModuleClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult

PROTOCOL = IoTHubTransportProvider.AMQP
CONNECTION_STRING = ""

def module_twin_callback(update_state, payload, user_context):
    print ("")
    print ("Twin callback called with:")
    print ("updateStatus: %s" % update_state )
    print ("context: %s" % user_context )
    print ("payload: %s" % payload )

try:
    module_client = IoTHubModuleClient(CONNECTION_STRING, PROTOCOL)
    module_client.set_module_twin_callback(module_twin_callback, 1234)

    print ("Waiting for incoming twin messages.  Hit Control-C to exit.")
    while True:

        time.sleep(1000000)

except IoTHubError as iothub_error:
    print ( "Unexpected error {0}".format(iothub_error) )
except KeyboardInterrupt:
    print ( "module client sample stopped" )
```


## <a name="next-steps"></a>后续步骤

若要继续了解 IoT 中心入门知识并浏览其他 IoT 方案，请参阅：

* [设备管理入门][lnk-device-management]
* [IoT 边缘入门][lnk-iot-edge]


<!-- Images. -->
[15]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG
<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/