---
title: include 文件
description: include 文件
services: azure-communication-services
author: dademath
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 07/28/2020
ms.topic: include
ms.custom: include file
ms.author: dademath
ms.openlocfilehash: 7fc1225a37353e43f5fb17f3394df167fb795bf7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91303297"
---
通过使用通信服务 C# 短信客户端库来发送短信，开启 Azure 通信服务使用旅程。

完成本快速入门会从你的 Azure 帐户中扣取最多几美分的费用。

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-net-pr/tree/feature/communication/sdk/communication/Azure.Communication.Sms#todo-update-to-public) | [Package (NuGet)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 适用于你的操作系统的最新版本 [.NET Core 客户端库](https://dotnet.microsoft.com/download/dotnet-core)。
- 活动的通信服务资源和连接字符串。 [创建通信服务资源](../../create-communication-resource.md)。
- 启用短信的电话号码。 [获取电话号码](../get-phone-number.md)。

### <a name="prerequisite-check"></a>先决条件检查

- 在终端或命令窗口中，运行 `dotnet` 命令来查看是否安装了 .NET 客户端。
- 若要查看与通信服务资源关联的电话号码，请登录到 [Azure 门户](https://portal.azure.com/)，找到通信服务资源，然后从左侧导航窗格中打开“电话号码”选项卡。

## <a name="setting-up"></a>设置

### <a name="create-a-new-c-application"></a>新建 C# 应用程序

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，使用 `dotnet new` 命令创建名为 `SmsQuickstart` 的新控制台应用。 此命令将创建包含单个源文件的简单“Hello World”C# 项目：**Program.cs**。

```console
dotnet new console -o SmsQuickstart
```

将目录更改为新创建的应用文件夹，并使用 `dotnet build` 命令编译应用程序。

```console
cd SmsQuickstart
dotnet build
```

### <a name="install-the-package"></a>安装包

仍在应用程序目录中时，使用 `dotnet add package` 命令安装适用于 .NET 包的 Azure 通信服务短信客户端库。

```console
dotnet add package Azure.Communication.Sms --version 1.0.0-beta.1
```

将 `using` 指令添加到 Program.cs 顶部以包括 `Azure.Communication` 命名空间。

```csharp

using Azure.Communication;
using Azure.Communication.Sms;

```

## <a name="object-model"></a>对象模型

以下类和接口处理适用于 C# 的 Azure 通信服务短信客户端库的某些主要功能。

| 名称                                       | 说明                                                                                                                                                       |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SmsClient     | 所有短信功能都需要此类。 使用订阅信息对其进行实例化，然后使用它发送短信。                           |
| SendSmsOptions | 此类提供用于配置传送报告的选项。 如果 enable_delivery_report 设置为 True，则在传送成功时将发出一个事件 |

## <a name="authenticate-the-client"></a>验证客户端

 在文本编辑器中打开 Program.cs，并将 `Main` 方法的主体替换为使用连接字符串初始化 `SmsClient` 的代码。 下面的代码从名为 `COMMUNICATION_SERVICES_CONNECTION_STRING` 的环境变量中检索资源的连接字符串。 了解如何[管理资源的连接字符串](../../create-communication-resource.md#store-your-connection-string)。


```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");

SmsClient smsClient = new SmsClient(connectionString);
```

## <a name="send-an-sms-message"></a>发送短信

通过调用 Send 方法发送短信。 将此代码添加到 Program.cs 中的 `Main` 方法末尾：

```csharp
smsClient.Send(
    from: new PhoneNumber("<leased-phone-number>"),
    to: new PhoneNumber("<to-phone-number>"),
    message: "Hello World via SMS",
    new SendSmsOptions { EnableDeliveryReport = true } // optional
);
```

应将 `<leased-phone-number>` 替换为与通信服务资源关联的启用短信的电话号码，将 `<to-phone-number>` 替换为要向其发送消息的电话号码。 所有电话号码参数都应遵循 [E.164 标准](../../../concepts/telephony-sms/plan-solution.md#optional-reading-international-public-telecommunication-numbering-plan-e164)。

`EnableDeliveryReport` 参数是一个可选参数，可用于配置传送报告。 这对于要在传送短信后发出事件的情况很有用。 请参阅[处理短信事件](../handle-sms-events.md)快速入门，了解如何为短信配置传送报告。

## <a name="run-the-code"></a>运行代码

从应用程序目录使用 `dotnet run` 命令运行应用程序。

```console
dotnet run
```
