---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 3a065e5cd6e951544b3147d5833b4ad300ae5e30
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164974"
---
[适用于 .NET 的 Microsoft Azure Configuration Manager 库](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) 提供用于分析配置文件中连接字符串的类。 无论客户端应用程序是在台式机、移动设备、Azure 虚拟机还是在 Azure 云服务中运行，都可以使用 [CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) 类分析配置设置。

若要引用 CloudConfigurationManager 包，请添加下面的 `using` 指令：

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
```

下面的示例演示了如何检索配置文件中的连接字符串：

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

可以选择使用 Azure Configuration Manager。 还可以使用 API，例如 .NET Framework 的 [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx) 类。

