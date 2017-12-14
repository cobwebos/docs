---
title: "使用 C 将 Windows 设备预配到远程监视 - Azure | Microsoft Docs"
description: "介绍如何使用在 Windows 上运行的以 C 编写的应用程序将设备连接到 Azure IoT 套件预配置远程监视解决方案。"
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 34e39a58-2434-482c-b3fa-29438a0c05e8
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: a6453c8d84b7025f1f510db5e675cde8685c23ed
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2017
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-windows"></a>将设备连接到远程监视预配置解决方案 (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

本教程介绍如何将物理设备连接到远程监视预配置解决方案。

## <a name="create-a-c-client-solution-on-windows"></a>在 Windows 上创建 C 客户端解决方案

与受约束设备上运行的大多数嵌入式应用程序一样，设备应用程序的客户端代码是用 C 语言编写的。在本教程中，将在运行 Windows 的计算机上生成应用程序。

### <a name="create-the-starter-project"></a>创建初学者项目

在 Visual Studio 2017 中创建初学者项目并添加 IoT 中心设备客户端 NuGet 包：

1. 在 Visual Studio 中，使用 Visual C++ **Windows 控制台应用程序**模板创建一个 C 控制台应用程序。 将该项目命名为 **RMDevice**。

    ![创建 Visual C++ Windows 控制台应用程序](media/iot-suite-connecting-devices/visualstudio01.png)

1. 在“解决方案资源管理器”中，删除文件 `stdafx.h`、`targetver.h` 和 `stdafx.cpp`。

1. 在“解决方案资源管理器”中，将文件 `RMDevice.cpp` 重命名为 `RMDevice.c`。

    ![显示已重命名的 RMDevice.c 文件的解决方案资源管理器](media/iot-suite-connecting-devices/visualstudio02.png)

1. 在“解决方案资源管理器”中，右键单击“RMDevice”项目，并单击“管理 NuGet 包”。 选择“浏览”，搜索并安装以下 NuGet 包：

    * Microsoft.Azure.IoTHub.Serializer
    * Microsoft.Azure.IoTHub.IoTHubClient
    * Microsoft.Azure.IoTHub.MqttTransport

    ![NuGet 包管理器显示已安装的 Microsoft.Azure.IoTHub 包](media/iot-suite-connecting-devices/visualstudio03.png)

1. 在“解决方案资源管理器”中，右键单击“RMDevice”项目，并选择“属性”打开该项目的“属性页”对话框。 有关详细信息，请参阅[设置 Visual C++ 项目属性](https://docs.microsoft.com/cpp/ide/working-with-project-properties)。

1. 选择“C/C++”文件夹，再选择“预编译标头”属性页。

1. 将“预编译标头”设置为“不使用预编译标头”。 然后选择“应用”。

    ![项目属性显示项目不使用预编译标头](media/iot-suite-connecting-devices/visualstudio04.png)

1. 选择“Linker”文件夹，再选择单击“输入”属性页。

1. 将 `crypt32.lib` 添加到“其他依赖项”属性。 若要保存项目属性值，请选择“确定”，然后再次选择“确定”。

    ![项目属性显示包括 crypt32.lib 的链接器](media/iot-suite-connecting-devices/visualstudio05.png)

### <a name="add-the-parson-json-library"></a>添加 Parson JSON 库

将 Parson JSON 库添加到 **RMDevice** 项目，并添加所需的 `#include` 语句：

1. 在计算机的适当文件夹中，使用以下命令克隆 Parson GitHub 存储库：

    ```cmd
    git clone https://github.com/kgabis/parson.git
    ```

1. 将 `parson.h` 和 `parson.c` 文件从 Parson 存储库的本地副本复制到 **RMDevice** 项目文件夹。

1. 在 Visual Studio 中，右键单击“RMDevice”项目，选择“添加”，再选择“现有项”。

1. 在“添加现有项”对话框中，选择“RMDevice”项目文件夹中的 `parson.h` 和 `parson.c` 文件。 若要将这两个文件添加到项目，请选择“添加”。

    ![解决方案资源管理器显示 parson.h 和 parson.c 文件](media/iot-suite-connecting-devices/visualstudio06.png)

1. 在 Visual Studio 中打开 `RMDevice.c` 文件。 将现有 `#include` 语句替换为以下代码：

    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include "parson.h"
    ```

    > [!NOTE]
    > 现在可以通过生成解决方案来验证该项目是否已设置正确的依赖项。

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>生成并运行示例

添加调用 **remote\_monitoring\_run** 函数的代码，然后生成并运行设备应用程序。

1. 若要调用 **remote\_monitoring\_run** 函数，请将 **main** 函数替换为以下代码：

    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. 选择“生成”，并单击“生成解决方案”以生成设备应用程序。 忽略有关 **gmtime** 函数的警告。

1. 在“解决方案资源管理器”中，右键单击“RMDevice”项目，选择“调试”，再选择“启动新实例”以运行示例。 控制台中的消息显示为：

    * 应用程序将示例遥测数据发送到预配置解决方案。
    * 接收在解决方案仪表板中设置的所需属性值。
    * 响应从解决方案仪表板调用的方法。

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
