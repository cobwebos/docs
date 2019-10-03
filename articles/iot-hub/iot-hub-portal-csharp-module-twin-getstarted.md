---
title: Azure IoT 中心模块标识和模块孪生（门户和 .NET）入门 | Microsoft Docs
description: 了解如何使用门户和 .NET 创建模块标识和更新模块孪生。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 395b1fd3301925db0607f775c6b7367979ba367b
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147433"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>使用门户和 .NET 设备创建 IoT 中心模块标识和模块孪生入门

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [模块标识和模块孪生](iot-hub-devguide-module-twins.md)类似于 Azure IoT 中心设备标识和设备孪生，但提供更精细的粒度。 尽管 Azure IoT 中心设备标识和设备克隆使后端应用程序可以配置设备并提供设备条件的可见性, 但模块标识和模块克隆为设备的单个组件提供这些功能。 在具有多个组件 (例如基于操作系统的设备或固件设备) 的支持设备上, 模块标识和模块孪生允许为每个组件提供独立的配置和条件。
>

本教程介绍：

* 如何在门户中创建模块标识。

* 如何使用 .NET 设备 SDK 更新设备中的模块克隆。

> [!NOTE]
> 有关可用于构建在设备和解决方案后端上运行的应用程序的 Azure IoT Sdk 的信息, 请参阅[Azure Iot sdk](iot-hub-devguide-sdks.md)。
>

## <a name="prerequisites"></a>先决条件

* Visual Studio。

* 有效的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="create-a-hub"></a>创建中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-hub"></a>在中心注册新设备

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>在门户中创建模块标识

在一个设备标识中，最多可以创建 20 个模块标识。 若要添加标识, 请执行以下步骤:

1. 对于在上一节中创建的设备, 选择 "**添加模块标识**" 以创建您的第一个模块标识。

1. 输入名称*myFirstModule*。 保存模块标识。

    ![添加模块标识](./media/iot-hub-portal-csharp-module-twin-getstarted/add-module-identity.png)

    新模块标识显示在屏幕底部。 选择它以查看模块标识详细信息。

    ![请参阅模块标识详细信息](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

保存**连接字符串-主键**。 在下一部分中使用它来设置设备上的模块。

## <a name="update-the-module-twin-using-net-device-sdk"></a>使用 .NET 设备 SDK 更新模块孪生

你已成功在 IoT 中心内创建模块标识。 现在尝试从模拟设备与云进行通信。 创建模块标识后，在 IoT 中心内隐式创建模块孪生。 在本节中，将在更新模块孪生报告属性的模拟设备上创建 .NET 控制台应用。

### <a name="create-a-visual-studio-project"></a>创建 Visual Studio 项目

若要创建更新模块克隆报告属性的应用, 请执行以下步骤:

1. 在 Visual Studio 中, 选择 "**创建新项目**", 然后选择 "**控制台应用 (.NET Framework)** ", 然后选择 "**下一步**"。

1. 在 "**配置新项目**" 中, 输入*UpdateModuleTwinReportedProperties*作为**项目名称**。 选择“创建”继续操作。

    ![配置为 visual studio 项目](./media/iot-hub-portal-csharp-module-twin-getstarted/configure-twins-project.png)

### <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>安装最新的 Azure IoT 中心 .NET 设备 SDK

模块标识和模块孪生为公共预览版。 它仅在 IoT 中心预发布设备 Sdk 中提供。 若要安装它, 请执行以下步骤:

1. 在 Visual Studio 中, 打开 "**工具** > " "**nuget 包管理器** > " "**管理解决方案的 NuGet 包**"。

1. 选择 "**浏览**", 然后选择 "**包括预发行**版"。 搜索 " *Microsoft Azure*"。 选择最新版本并安装。

    ![安装 Azure IoT 中心 .NET 服务 SDK 预览版](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

    现在可以访问所有模块功能。

### <a name="get-your-module-connection-string"></a>获取模块连接字符串

你需要控制台应用的模块连接字符串。 请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 导航到 IoT 中心, 然后选择 " **Iot 设备**"。 打开**为 myfirstdevice** , 你会看到**myFirstModule**已成功创建。

1. 在 "**模块标识**" 下选择 " **myFirstModule** "。 在 "**模块标识详细信息**" 中, 复制**连接字符串 (主键)** 。

    ![Azure 门户模块详细信息](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

### <a name="create-updatemoduletwinreportedproperties-console-app"></a>创建 UpdateModuleTwinReportedProperties 控制台应用

若要创建应用, 请执行以下步骤:

1. 在 Program.cs 文件顶部添加以下 `using` 语句：

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

4. 最后, 将**Main**方法替换为以下代码:

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
  
  可以使用**F5**生成并运行此应用。

此代码示例演示如何检索模块孪生和借助 AMQP 协议更新报告属性。 在公开预览版中，我们仅支持通过 AMQP 进行模块孪生操作。

## <a name="next-steps"></a>后续步骤

若要继续了解 IoT 中心入门知识并浏览其他 IoT 方案，请参阅：

* [通过 .NET 备份和 .NET 设备开始使用 IoT 中心模块标识和模块孪生](iot-hub-csharp-csharp-module-twin-getstarted.md)

* [IoT Edge 入门](../iot-edge/tutorial-simulate-device-linux.md)
