---
title: Azure IoT 中心设备流 Node.js 快速入门（预览）| Microsoft Docs
description: 在本快速入门中，你将运行一个 Node.js 服务端应用程序，以便通过设备流与 IoT 设备通信。
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 1e7efe28918cafb3fa9547c144be3360768d549c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58079889"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>快速入门：通过 IoT 中心设备流在 Node.js 中与设备应用程序通信（预览）

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Microsoft Azure IoT 中心目前支持设备流作为[预览版功能](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

服务和设备应用程序可以使用 [IoT 中心设备流](./iot-hub-device-streams-overview.md)以安全且防火墙友好的方式进行通信。 在公共预览期，Node.js SDK 仅支持服务端的设备流。 因此，本快速入门只介绍如何运行服务端应用程序。 应该运行 [C 快速入门](./quickstart-device-streams-echo-c.md)或 [C# 快速入门](./quickstart-device-streams-echo-csharp.md)指南中随附的设备端应用程序。

本快速入门中的服务端 Node.js 应用程序具有以下功能：

* 创建发往 IoT 设备的设备流。

* 从命令行读取输入，将其发送到设备应用程序，后者会将其回显。

代码将演示设备流的启动过程，以及如何用其来发送和接收数据。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。


## <a name="prerequisites"></a>先决条件

目前仅以下区域中创建的 IoT 中心支持设备流预览：

  - 美国中部
  - **美国中部 EUAP**

若要运行本快速入门中所述的服务端应用程序，需要在开发计算机上安装 Node.js v4.x.x 或更高版本。

可从 [Node.js.org](https://nodejs.org) 下载适用于多个平台的 Node.js。

可以使用以下命令验证开发计算机上 Node.js 当前的版本：

```
node --version
```

如果尚未进行此操作，请从 https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip 下载示例 Node.js 项目并提取 ZIP 存档。


## <a name="create-an-iot-hub"></a>创建 IoT 中心

如果已完成上一[快速入门：将遥测数据从设备发送到 IoT 中心](quickstart-send-telemetry-node.md)，则可以跳过此步骤。

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]


## <a name="register-a-device"></a>注册设备

如果已完成上一[快速入门：将遥测数据从设备发送到 IoT 中心](quickstart-send-telemetry-node.md)，则可以跳过此步骤。

必须先将设备注册到 IoT 中心，然后该设备才能进行连接。 在本快速入门中，将使用 Azure Cloud Shell 来注册模拟设备。

1. 在 Azure Cloud Shell 中运行以下命令，以添加 IoT 中心 CLI 扩展并创建设备标识。 

   **YourIoTHubName**：将下面的占位符替换为你为 IoT 中心选择的名称。

   **MyDevice**：这是为注册的设备提供的名称。 如示例中所示使用 MyDevice。 如果为设备选择不同名称，则可能还需要在本文中从头至尾使用该名称，并在运行示例应用程序之前在其中更新设备名称。

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. 还需一个服务连接字符串，以便后端应用程序能够连接到 IoT 中心并检索消息。 以下命令检索 IoT 中心的服务连接字符串：

    **YourIoTHubName**：将下面的占位符替换为你为 IoT 中心选择的名称。

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    记下返回的值，如下所示：

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`


## <a name="communicate-between-device-and-service-via-device-streams"></a>通过设备流在设备和服务之间通信

### <a name="run-the-device-side-application"></a>运行设备端应用程序

如前所述，IoT 中心 Node.js SDK 仅支持服务端的设备流。 对于设备端应用程序，请使用 [C 快速入门](./quickstart-device-streams-echo-c.md)或 [C# 快速入门](./quickstart-device-streams-echo-csharp.md)指南中随附的设备程序。 在继续下一步之前，请确保设备端应用程序正在运行。


### <a name="run-the-service-side-application"></a>运行服务端应用程序

假设设备端应用程序正在运行，请遵循以下步骤运行以 Node.js 编写的服务端应用程序：

- 以环境变量的形式提供服务凭据和设备 ID。
  ```
  # In Linux
  export IOTHUB_CONNECTION_STRING="<provide_your_service_connection_string>"
  export STREAMING_TARGET_DEVICE="MyDevice"

  # In Windows
  SET IOTHUB_CONNECTION_STRING=<provide_your_service_connection_string>
  SET STREAMING_TARGET_DEVICE=MyDevice
  ```
  将 `MyDevice` 更改为你给设备选择的设备 ID。

- 导航到解压缩的项目文件夹中的 `Quickstarts/device-streams-service`，并使用节点来运行示例。
  ```
  cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
  
  # Install the preview service SDK, and other dependencies
  npm install azure-iothub@streams-preview
  npm install

  node echo.js
  ```

在最后一步结束时，服务端程序会启动一个发往设备的流，在该流建立后就会通过其将字符串缓冲区发送到服务。 在此示例中，服务端程序直接读取终端的 stdin 并将其发送到设备，后者然后将其回显。 这演示了两个应用程序之间成功进行的双向通信。

服务端的控制台输出：![alt 文本](./media/quickstart-device-streams-echo-nodejs/service-console-output.PNG "服务端的控制台输出")


然后可以再次按 Enter，将程序终止。


## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]


## <a name="next-steps"></a>后续步骤

在本快速入门中，你设置了一个 IoT 中心、注册了一个设备、在设备和服务端的应用程序之间建立了一个设备流，并通过该流在应用程序之间来回发送数据。

请使用以下链接详细了解设备流：

> [!div class="nextstepaction"]
> [设备流概述](./iot-hub-device-streams-overview.md)
