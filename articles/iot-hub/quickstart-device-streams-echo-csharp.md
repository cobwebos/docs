---
title: 通过 Azure IoT 中心设备流（预览版）使用 C# 与设备应用进行通信 | Microsoft Docs
description: 在本快速入门中，我们将运行两个使用通过 IoT 中心建立的设备流通信的示例 C# 应用程序。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: de581362371e28523c99f961dfdb5c2009901343
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446110"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>快速入门：通过 IoT 中心设备流（预览版）使用 C# 与设备应用程序进行通信

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT 中心目前支持设备流作为[预览版功能](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

服务和设备应用程序可以使用 [IoT 中心设备流](./iot-hub-device-streams-overview.md)以安全且防火墙友好的方式进行通信。 本快速入门涉及两个 C# 应用程序，它们利用设备流来回发送数据（回显）。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

* 目前仅以下区域中创建的 IoT 中心支持设备流预览：
  * 美国中部
  * 美国中部 EUAP

* 本快速入门中运行的两个示例应用程序是使用 C# 编写的。 开发计算机上需要有 .NET Core SDK 2.1.0 或更高版本。
  * [从 .NET 下载适用于多个平台的 .NET Core SDK](https://www.microsoft.com/net/download/all)。
  * 使用以下命令验证开发计算机上 C# 的当前版本：

   ```
   dotnet --version
   ```

* 运行以下命令，将用于 Azure CLI 的 Azure IoT 扩展添加到 Cloud Shell 实例。 IOT 扩展会将 IoT 中心、IoT Edge 和 IoT 设备预配服务 (DPS) 特定的命令添加到 Azure CLI。

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

* [下载示例 C# 项目](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)并解压缩 ZIP 存档。 需要在设备端和服务端执行此操作。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>注册设备

必须先将设备注册到 IoT 中心，然后该设备才能进行连接。 在本部分，我们将使用 Azure Cloud Shell 来注册模拟设备。

1. 若要创建设备标识，请在 Cloud Shell 中运行以下命令：

   > [!NOTE]
   > * 请将 *YourIoTHubName* 占位符替换为你为 IoT 中心选择的名称。
   > * 如示例中所示使用 *MyDevice*。 它是为注册的设备提供的名称。 如果为设备选择其他名称，请在本文中从头至尾使用该名称，并在运行示例应用程序之前在其中更新设备名称。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

1. 若要获取刚刚注册的设备的*设备连接字符串*，请在 Cloud Shell 中运行以下命令：

   > [!NOTE]
   > 请将 *YourIoTHubName* 占位符替换为你为 IoT 中心选择的名称。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    请记下设备连接字符串，稍后需要在本快速入门中用到它。 如以下示例所示：

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

3. 此外，需要使用 IoT 中心的服务连接字符串才能让服务端应用程序连接到 IoT 中心并建立设备流。  以下命令检索 IoT 中心的此值：

   > [!NOTE]
   > 请将 *YourIoTHubName* 占位符替换为你为 IoT 中心选择的名称。

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    请记下返回的值，因为稍后要在本快速入门中用到它。 如以下示例所示：

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>通过设备流在设备与服务之间通信

在本部分中，你将运行设备端应用程序和服务器端应用程序并在两者之间进行通信。

### <a name="run-the-service-side-application"></a>运行服务端应用程序

转到解压缩的项目文件夹中的 *iot-hub/Quickstarts/device-streams-echo/service* 目录。 请保留以下信息：

| 参数名称 | 参数值 |
|----------------|-----------------|
| `ServiceConnectionString` | 提供 IoT 中心的服务连接字符串。 |
| `DeviceId` | 提供之前创建的设备的 ID（例如 *MyDevice*）。 |

按如下所示编译并运行代码：

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "<ServiceConnectionString>" "<MyDevice>"

# In Windows
dotnet run <ServiceConnectionString> <MyDevice>
```

> [!NOTE]
> 如果设备端应用程序未及时响应，则会发生超时。

### <a name="run-the-device-side-application"></a>运行设备端应用程序

转到解压缩的项目文件夹中的 *iot-hub/Quickstarts/device-streams-echo/device* 目录。 请保留以下信息：

| 参数名称 | 参数值 |
|----------------|-----------------|
| `DeviceConnectionString` | 提供 IoT 中心的设备连接字符串。 |

按如下所示编译并运行代码：

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "<DeviceConnectionString>"

# In Windows
dotnet run <DeviceConnectionString>
```

最后一个步骤结束时，服务端应用程序会向设备发起流。 建立流后，应用程序会通过该流将一个字符串缓冲区发送到服务。 在此示例中，服务端应用程序直接将相同的数据回显到设备，表明已成功地在两个应用程序之间建立双向通信。

设备端的控制台输出：

![设备端的控制台输出](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

服务端的控制台输出：

![服务端的控制台输出](./media/quickstart-device-streams-echo-csharp/service-console-output.png)

通过该流发送的流量将通过 IoT 中心以隧道方式进行传输，而不是直接发送。 [设备流优势](./iot-hub-device-streams-overview.md#benefits)中详细介绍了所提供的优势。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，我们设置了一个 IoT 中心、注册了一个设备、在设备和服务端的 C# 应用程序之间建立了一个设备流，并通过该流在应用程序之间来回发送数据。

若要详细了解设备流，请参阅：

> [!div class="nextstepaction"]
> [设备流概述](./iot-hub-device-streams-overview.md)
