---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 5102866cdda51ef545fd95b32946cb17c6e40a3c
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038169"
---
[适用于 .NET 的 Microsoft Azure Configuration Manager 库](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) 提供用于分析配置文件中连接字符串的类。 [CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx)类分析配置设置。 它分析桌面、移动设备、Azure 虚拟机或 Azure 云服务中运行的客户端应用程序的设置。

若要引用 `CloudConfigurationManager` 包，请添加以下 `using` 指令：

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage;
```

下面的示例演示了如何检索配置文件中的连接字符串：

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

可以选择使用 Azure Configuration Manager。 还可以使用 API，例如 .NET Framework 的[ConfigurationManager 类](/dotnet/api/system.configuration.configurationmanager)。
