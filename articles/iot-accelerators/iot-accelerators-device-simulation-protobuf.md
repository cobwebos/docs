---
title: 对设备模拟使用协议缓冲区 - Azure | Microsoft Docs
description: 本操作指南介绍如何使用协议缓冲区来序列化设备模拟解决方案加速器发出的遥测数据。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: bc08cd5183bcaac6cb77ccb0938b07893f082862
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250213"
---
# <a name="serialize-telemetry-using-protocol-buffers"></a>使用协议缓冲区将遥测数据序列化

协议缓冲区 (Protobuf) 是结构化数据的二进制序列化格式。 Protobuf 强调简易性和性能，目标是比 XML 更小更快。

设备模拟支持 **proto3** 版本的协议缓冲区语言。

由于 Protobuf 需要使用编译的代码来序列化数据，因此必须生成自定义版本的设备模拟。

本操作指南中的步骤介绍如何：

1. 准备开发环境
1. 指定在设备模型中使用 Protobuf 格式
1. 定义 Protobuf 格式
1. 生成 Protobuf 类
1. 本地测试

## <a name="prerequisites"></a>必备条件

若要遵循本操作指南中的步骤，需要：

* Visual Studio Code。 可以[下载适用于 Mac、Linux 和 Windows 的 Visual Studio Code](https://code.visualstudio.com/download)。
* .NET Core。 可以下载[适用于 Mac、Linux 和 Windows 的 .NET Core](https://www.microsoft.com/net/download)。
* Postman。 可以下载[适用于 Mac、Windows 或 Linux 的 Postman](https://www.getpostman.com/apps)。
* [部署到 Azure 订阅的 IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。 需要 IoT 中心的连接字符串才能完成本指南中的步骤。 从 Azure 门户可获取连接字符串。
* [已部署到 Azure 订阅的 Cosmos DB 数据库](../cosmos-db/create-sql-api-dotnet.md#create-account)，该数据库使用 SQL API，并配置为[非常一致性](../cosmos-db/manage-account.md)。 需要 Cosmos DB 数据库的连接字符串才能完成本指南中的步骤。 从 Azure 门户可获取连接字符串。
* [已部署到 Azure 订阅的 Azure 存储帐户](../storage/common/storage-account-create.md)。 需要该存储帐户的连接字符串才能完成本指南中的步骤。 从 Azure 门户可获取连接字符串。

## <a name="prepare-your-development-environment"></a>准备开发环境

完成以下任务以准备开发环境：

* 下载设备模拟微服务的源。
* 下载存储适配器微服务的源。
* 在本地运行存储适配器微服务。

本文中的说明假定使用 Windows 操作系统。 如果使用另一操作系统，则可能需要调整某些文件路径和命令以适应环境。

### <a name="download-the-microservices"></a>下载微服务

从 GitHub 下载[远程监视微服务](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip)并将其解压缩到本地计算机上的适当位置。 此存储库包含本操作指南所需的存储适配器微服务。

从 GitHub 下载[设备模拟微服务](https://github.com/Azure/device-simulation-dotnet/archive/master.zip)并将其解压缩到本地计算机上的适当位置。

### <a name="run-the-storage-adapter-microservice"></a>运行存储适配器微服务

在 Visual Studio Code 中打开 **remote-monitoring-services-dotnet-master\storage-adapter** 文件夹。 单击任意“还原”按钮，修复未解决的依赖项。

打开 **.vscode/launch.json** 文件，并将 Cosmos DB 连接字符串分配到 **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** 环境变量。

> [!NOTE]
> 在计算机本地运行微服务时，它仍然需要 Azure 中的 Cosmos DB 实例才能正常运行。

若要在本地运行存储适配器微服务，请单击“调试” **“开始调试”\>** 。

Visual Studio Code 中的“终端”窗口显示正在运行的微服务的输出，包括 Web 服务运行状况检查的 URL：<http://127.0.0.1:9022/v1/status>。 导航到此地址时，状态应显示为“正常: 活动且正常”。

在完成后续步骤时，让存储适配器微服务继续在 Visual Studio Code 的此实例中运行。

## <a name="define-your-device-model"></a>定义设备模型

在 Visual Studio Code 的新实例中打开从 GitHub 下载的 device-simulation-dotnet-master 文件夹。 单击任意“还原”按钮，修复任何未解决的依赖项。

在本操作指南中，我们将为资产跟踪器创建新的设备模型：

1. 在 **Services\data\devicemodels** 文件夹中，创建名为 **assettracker-01.json** 的新设备模型文件。

1. 在设备模型 **assettracker-01.json** 文件中定义设备功能。 Protobuf 设备模型的 telemetry 节必须：

   * 包含为设备生成的 Protobuf 类的名称。 以下部分将介绍如何生成此类。
   * 指定 Protobuf 作为消息格式。

     ```json
     {
     "SchemaVersion": "1.0.0",
     "Id": "assettracker-01",
     "Version": "0.0.1",
     "Name": "Asset Tracker",
     "Description": "An asset tracker with location, temperature, and humidity",
     "Protocol": "AMQP",
     "Simulation": {
       "InitialState": {
         "online": true,
         "latitude": 47.445301,
         "longitude": -122.296307,
         "temperature": 38.0,
         "humidity": 62.0
       },
       "Interval": "00:01:00",
       "Scripts": [
         {
           "Type": "javascript",
           "Path": "assettracker-01-state.js"
         }
       ]
     },
     "Properties": {
       "Type": "AssetTracker",
       "Location": "Field",
       "Latitude": 47.445301,
       "Longitude": -122.296307
     },
     "Telemetry": [
       {
         "Interval": "00:00:10",
         "MessageTemplate": "{\"latitude\":${latitude},\"longitude\":${longitude},\"temperature\":${temperature},\"humidity\":${humidity}}",
         "MessageSchema": {
           "Name": "assettracker-sensors;v1",
           "ClassName": "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf.AssetTracker",
           "Format": "Protobuf",
           "Fields": {
             "latitude": "double",
             "longitude": "double",
             "temperature": "double",
             "humidity": "double"
           }
         }
       }
     ]
     }
     ```

### <a name="create-device-behaviors-script"></a>创建设备行为脚本

编写用于定义设备行为方式的行为脚本。 有关详细信息，请参阅[创建高级模拟设备](iot-accelerators-device-simulation-advanced-device.md)。

## <a name="define-your-protobuf-format"></a>定义 Protobuf 格式

创建设备模型并确定消息格式后，可以创建 **proto** 文件。 在 **proto** 文件中，添加：

* 与设备模型中 `csharp_namespace`ClassName**属性匹配的**。
* 要序列化的每个数据结构的消息。
* 该消息中每个字段的名称和类型。

1. 在 **Services\Models\Protobuf\proto** 文件夹中创建名为 **assettracker.proto** 的新文件。

1. 如下所示，在 **proto** 文件中定义语法、命名空间和消息架构：

    ```proto
    syntax = "proto3";

    option csharp_namespace = "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf";

    message AssetTracker {
      double latitude=1;
      double longitude=2;
      double temperature=5;
      double humidity=6;
    }
    ```

每个元素中的 `=1` 和 `=2` 标记指定字段在二进制编码中使用的唯一标记。 编号 1 到 15 所要编码的字节数比更高编号要少 1 个。

## <a name="generate-the-protobuf-class"></a>生成 Protobuf 类

创建 **proto** 文件后，下一步是生成用于读取和写入消息的类。 若要完成此步骤，需要 **Protoc** Protobuf 编译器。

1. [从 GitHub 下载 Protobuf 编译器](https://github.com/protocolbuffers/protobuf/releases/download/v3.4.0/protoc-3.4.0-win32.zip)

1. 运行编译器，并指定 **proto** 文件的源目录、目标目录和名称。 例如：

    ```cmd
    protoc -I c:\temp\device-simulation-dotnet-master\Services\Models\Protobuf\proto --csharp_out=C:\temp\device-simulation-dotnet-master\Services\Models\Protobuf assettracker.proto
    ```

    此命令将在 **Services\Models\Protobuf** 文件夹中生成 **Assettracker.cs** 文件。

## <a name="test-protobuf-locally"></a>在本地测试 Protobuf

在本部分，我们将测试在前面部分中本地创建的资产跟踪器设备。

### <a name="run-the-device-simulation-microservice"></a>运行设备模拟微服务

打开 **.vscode/launch.json** 文件并执行以下操作：

* 将 IoT 中心连接字符串分配到 **PCS\_IOTHUB\_CONNSTRING** 环境变量。
* 将存储帐户连接字符串分配到 **PCS\_AZURE\_STORAGE\_ACCOUNT** 环境变量。
* 将 cosmos DB 连接字符串分配到 **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** 环境变量。

打开 **WebService/Properties/launchSettings.json** 文件并执行以下操作：

* 将 IoT 中心连接字符串分配到 **PCS\_IOTHUB\_CONNSTRING** 环境变量。
* 将存储帐户连接字符串分配到 **PCS\_AZURE\_STORAGE\_ACCOUNT** 环境变量。
* 将 cosmos DB 连接字符串分配到 **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** 环境变量。

按如下所示打开 **WebService/appsettings.ini** 文件并修改设置：

#### <a name="configure-the-solution-to-include-your-new-device-model-files"></a>将解决方案配置为包含新的设备模型文件

默认情况下，新设备模型的 JSON 和 JS 文件不会复制到生成的解决方案。 需要显式包含这些文件。

将一个条目添加到所要包含的每个文件的 **services\services.csproj** 文件。 例如：

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

若要在本地运行微服务，请单击“调试” **“开始调试”\>** 。

Visual Studio Code 中的“终端”窗口显示正在运行的微服务的输出。

在完成后续步骤时，让设备模拟微服务继续在 Visual Studio Code 的此实例中运行。

### <a name="set-up-a-monitor-for-device-events"></a>设置设备事件的监视器

在本部分中，使用 Azure CLI 设置事件监视器，以查看从连接到 IoT 中心的设备发送的遥测。

以下脚本假定 IoT 中心的名称为 device-simulation-test。

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

测试模拟设备时，让事件监视器继续运行。

### <a name="create-a-simulation-with-the-asset-tracker-device-type"></a>使用资产跟踪器设备类型创建模拟

在本部分，我们将使用 Postman 工具请求设备模拟微服务利用资产跟踪器设备类型运行模拟。 Postman 是一种可实现将 REST 请求发送到 Web 服务的工具。

要设置 Postman，请执行以下操作：

1. 在本地计算机上打开 Postman。

1. 单击“文件” **“导入”\>** 。 然后单击“选择文件”。

1. 选择“Azure IoT 设备模拟解决方案 accelerator.postman**collection”和“Azure IoT 设备模拟解决方案 accelerator.postman\_environment”，然后单击“打开”** **\_** 。

1. 展开“Azure IoT 设备模拟解决方案加速器”以查看可发送的请求。

1. 单击“无环境”，然后选择“Azure IoT 设备模拟解决方案加速器”。

现在，已在 Postman 工作区中加载可用于与设备模拟微服务进行交互的集合和环境。

要配置和运行模拟，请执行以下操作：

1. 在 Postman 集合中，选择“创建资产跟踪器模拟”并单击“发送”。 此请求将创建模拟的资产跟踪器设备类型的四个实例。

1. Azure CLI 窗口中的事件监视器输出会显示来自模拟设备的遥测数据。

要停止模拟，请选择 Postman 中的“停止模拟”请求并单击“发送”。

### <a name="clean-up-resources"></a>清理资源

可在相应的 Visual Studio Code 实例中停止这两个本地运行的微服务（“调试” **“停止调试”）\>** 。

如果不再需要 IoT 中心和 Cosmos DB 实例，请从 Azure 订阅中将其删除，以避免产生任何不必要的费用。

## <a name="iot-hub-support"></a>IoT 中心支持

许多 IoT 中心功能原生并不支持 Protobuf 或其他二进制格式。 例如，无法基于消息有效负载路由消息，因为 IoT 中心无法处理消息有效负载。 但是，可以基于消息标头路由消息。

## <a name="next-steps"></a>后续步骤

了解如何将设备模拟自定义为使用 Protobuf 发送遥测数据之后，接下来请了解如何[将自定义映像部署到云中](iot-accelerators-device-simulation-deploy-image.md)。
