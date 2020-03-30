---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 5102866cdda51ef545fd95b32946cb17c6e40a3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "72038169"
---
[适用于 .NET 的 Microsoft Azure Configuration Manager 库](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) 提供用于分析配置文件中连接字符串的类。 [云配置管理器](https://msdn.microsoft.com/library/azure/mt634650.aspx)类解析配置设置。 它分析在桌面、移动设备、Azure 虚拟机或 Azure 云服务中运行的客户端应用程序的设置。

要引用包`CloudConfigurationManager`，添加以下`using`指令：

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

可以选择使用 Azure Configuration Manager。 您还可以使用 API，如 .NET 框架的[配置管理器类](/dotnet/api/system.configuration.configurationmanager)。
