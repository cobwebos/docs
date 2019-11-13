---
title: Azure IoT 中心模块身份 & 模块（门户和 .NET）
description: 了解如何使用门户和 .NET 创建模块标识和更新模块孪生。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 289c269100eb6ab672bb8d60562c1fa77d8d091a
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954615"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>使用门户和 .NET 设备创建 IoT 中心模块标识和模块孪生入门

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [模块标识和模块孪生](iot-hub-devguide-module-twins.md)类似于 Azure IoT 中心设备标识和设备孪生，但提供更精细的粒度。 Azure IoT 中心设备标识和设备孪生允许后端应用程序配置设备并提供设备条件的可见性，而模块标识和模块孪生为设备的各个组件提供这些功能。 在支持多个组件的设备上（例如基于操作系统的设备或固件设备），模块标识和模块孪生允许每个部件拥有独立的配置和条件。
>

本教程介绍：

* 如何在门户中创建模块标识。

* 如何使用 .NET 设备 SDK 更新设备的模块孪生。

> [!NOTE]
> 有关可用于构建在设备和解决方案后端上运行的应用程序的 Azure IoT Sdk 的信息，请参阅[Azure Iot sdk](iot-hub-devguide-sdks.md)。
>

## <a name="prerequisites"></a>先决条件

* Visual Studio。

* 有效的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个 [免费帐户](https://azure.microsoft.com/pricing/free-trial/) 。

## <a name="create-a-hub"></a>创建中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-hub"></a>在中心注册新设备

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>在门户中创建模块标识

在一个设备标识中，最多可以创建 20 个模块标识。 若要添加标识，请执行以下步骤：

1. 对于在上一部分创建的设备，请选择“添加模块标识”，以便创建第一个模块标识。

1. 输入名称“myFirstModule”。 保存模块标识。

    ![添加模块标识](./media/iot-hub-portal-csharp-module-twin-getstarted/add-module-identity.png)

    新的模块标识将出现在屏幕底部。 选择它即可查看模块标识详细信息。

    ![查看模块标识详细信息](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

保存“连接字符串 - 主密钥”。 可以在下一部分使用它，以便在设备上设置模块。

## <a name="update-the-module-twin-using-net-device-sdk"></a>使用 .NET 设备 SDK 更新模块孪生

你已成功在 IoT 中心内创建模块标识。 现在尝试从模拟设备与云进行通信。 创建模块标识后，在 IoT 中心内隐式创建模块孪生。 在本节中，将在更新模块孪生报告属性的模拟设备上创建 .NET 控制台应用。

### <a name="create-a-visual-studio-project"></a>创建 Visual Studio 项目

若要创建一个应用来更新模块孪生报告属性，请执行以下步骤：

1. 在 Visual Studio 中依次选择“创建新项目”、“控制台应用(.NET Framework)”、“下一步”。

1. 在“配置新项目”中，输入 *UpdateModuleTwinReportedProperties* 作为“项目名称”。 选择“创建”继续操作。

    ![配置 Visual Studio 项目](./media/iot-hub-portal-csharp-module-twin-getstarted/configure-twins-project.png)

### <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>安装最新的 Azure IoT 中心 .NET 设备 SDK

模块标识和模块孪生为公共预览版。 仅在 IoT 中心预发行设备 SDK 中提供它。 若要安装它，请执行以下步骤：

1. 在 Visual Studio 中，打开“工具” **“NuGet 包管理器”** “管理解决方案的 NuGet 包”。 >  > 

1. 选择“浏览”，然后选择“包括预发行版”。 搜索 *Microsoft.Azure.Devices.Client*。 选择最新版本并安装。

    ![安装 Azure IoT 中心 .NET 服务 SDK 预览版](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

    现在即可访问所有模块功能。

### <a name="get-your-module-connection-string"></a>获取模块连接字符串

需要控制台应用的模块连接字符串。 执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 导航到 IoT 中心并选择“IoT 设备”。 打开 **myFirstDevice**，可以看到 **myFirstModule** 已成功创建。

1. 在“模块标识”下选择“myFirstModule”。 在“模块标识详细信息”中复制“连接字符串(主密钥)”。

    ![Azure 门户模块详细信息](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

### <a name="create-updatemoduletwinreportedproperties-console-app"></a>创建 UpdateModuleTwinReportedProperties 控制台应用

若要创建应用，请按以下步骤操作：

1. 在 `using`Program.cs**文件顶部添加以下** 语句：

  ```csharp
  using Microsoft.Azure.Devices.Client;
  using Microsoft.Azure.Devices.Shared;
  using Newtonsoft.Json;
  ```

2. 将以下字段添加到 Program 类。 将占位符值替换为模块连接字符串。

  ```csharp
  private const string ModuleConnectionString = "<Your module connection string>";
  private static ModuleClient Client = null;
  ```

3. 将以下方法“OnDesiredPropertyChanged”添加到“Program”类：

  ```csharp
  private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
      {
          Console.WriteLine("desired property change:");
          Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
          Console.WriteLine("Sending current time as reported property");
          TwinCollection reportedProperties = new TwinCollection
          {
              ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
          };
  
          await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
      }
  ```

4. 最后，将 **Main**方法替换为以下代码：

  ```csharp
  static void Main(string[] args)
  {
      Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;
  
      try
      {
          Client = ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
          Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
          Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();
  
          Console.WriteLine("Retrieving twin");
          var twinTask = Client.GetTwinAsync();
          twinTask.Wait();
          var twin = twinTask.Result;
          Console.WriteLine(JsonConvert.SerializeObject(twin));
  
          Console.WriteLine("Sending app start time as reported property");
          TwinCollection reportedProperties = new TwinCollection();
          reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;
  
          Client.UpdateReportedPropertiesAsync(reportedProperties);
      }
      catch (AggregateException ex)
      {
          Console.WriteLine("Error in sample: {0}", ex);
      }
  
      Console.WriteLine("Waiting for Events.  Press enter to exit...");
      Console.ReadKey();
      Client.CloseAsync().Wait();
  }
  
  private static void ConnectionStatusChangeHandler(ConnectionStatus status, ConnectionStatusChangeReason reason)
  {
      Console.WriteLine($"Status {status} changed: {reason}");
  }
  ```
  
  可以通过 **F5** 生成并运行此应用。

此代码示例演示如何检索模块孪生和借助 AMQP 协议更新报告属性。 在公开预览版中，我们仅支持通过 AMQP 进行模块孪生操作。

## <a name="next-steps"></a>后续步骤

若要继续了解 IoT 中心入门知识并浏览其他 IoT 方案，请参阅：

* [通过 .NET 备份和 .NET 设备开始使用 IoT 中心模块标识和模块孪生](iot-hub-csharp-csharp-module-twin-getstarted.md)

* [IoT Edge 入门](../iot-edge/tutorial-simulate-device-linux.md)
