---
title: "用于 .NET Framework 和 .NET Core 的 Azure 库 | Microsoft Docs"
description: "所有用于 .NET Framework 和 .NET Core 的 Azure 库与 NuGet 包的列表。"
keywords: "Azure.NET, SDK, Azure.NET API 参考, Azure.NET 类库, Azure NuGet, Azure Core"
author: camsoper
manager: douge
ms.author: casoper
ms.date: 04/06/2016
ms.topic: managed-reference
ms.prod: azure
ms.technology: azure
ms.devlang: dotnet
ms.assetid: 
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: fd38e1c9b0ebb89d930274ab92b63f4fb0219ca0
ms.lasthandoff: 04/13/2017

---

# <a name="azure-libraries-for-net"></a>用于 .NET 的 Azure 库

使用用于 Azure 的 .NET SDK 中的库可在应用程序中管理和使用 Azure 服务。

## <a name="packages"></a>包

> [!TIP]
> [Fluent 库](https://azure.microsoft.com/blog/simpler-azure-management-libraries-for-net/)（以 **\*.Fluent** 结尾）通过提供面向对象的更高级 API（已针对可读性和可写性进行了优化），来改善开发人员体验。 它们阐明了什么是必需的，什么是可选的，以及什么是不可修改的。 这些库可以与非 Fluent 库并行运行，因此如果你喜欢该语法，请使用 Fluent 包。 [Microsoft.Azure.Management.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent) 是包含所有 Fluent 管理库的汇总包。

服务 | 与服务交互 | 管理服务资源
--------|---------------------------|-------------------------
[Analysis Services](/azure/analysis-services/) | | [Microsoft.Azure.Management.Analysis](https://www.nuget.org/packages/Microsoft.Azure.Management.Analysis)
[ApiManagement](/azure/api-management/) | | [Microsoft.Azure.Management.ApiManagement](https://www.nuget.org/packages/Microsoft.Azure.Management.ApiManagement)
[授权](/rest/api/authorization) | | [Microsoft.Azure.Management.Authorization](https://www.nuget.org/packages/Microsoft.Azure.Management.Authorization)
[自动化](/azure/automation/) | | [Microsoft.Azure.Management.Automation](https://www.nuget.org/packages/Microsoft.Azure.Management.Automation)
[Azure Active Directory](/azure/active-directory) | [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) | 
[备份](/azure/backup/) | | [Microsoft.Azure.Management.RecoveryServices.Backup](https://www.nuget.org/packages/Microsoft.Azure.Management.RecoveryServices.Backup)
[批处理](/azure/batch/) | [Azure.Batch](https://www.nuget.org/packages/Azure.Batch) | [Microsoft.Azure.Management.Batch.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Batch.Fluent)<br/>[Microsoft.Azure.Management.Batch](https://www.nuget.org/packages/Microsoft.Azure.Management.Batch)
[计费](/azure/billing/) | | [Microsoft.Azure.Management.Billing](https://www.nuget.org/packages/Microsoft.Azure.Management.Billing)
[CDN](/azure/cdn/) | | [Microsoft.Azure.Management.Cdn.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Cdn.Fluent)<br/>[Microsoft.Azure.Management.Cdn](https://www.nuget.org/packages/Microsoft.Azure.Management.Cdn)
[认知服务](/azure/cognitive-services/) | | [Microsoft.Azure.Management.CognitiveServices](https://www.nuget.org/packages/Microsoft.Azure.Management.CognitiveServices)
[商务](/azure/billing/billing-usage-rate-card-overview) | | [Microsoft.Azure.Commerce.UsageAggregates](https://www.nuget.org/packages/Microsoft.Azure.Commerce.UsageAggregates)
[容器注册表](/azure/container-registry) | | [Microsoft.Azure.Management.ContainerRegistry](https://www.nuget.org/packages/Microsoft.Azure.Management.ContainerRegistry)
[Customer Insights](/dynamics365/customer-insights) | | [Microsoft.Azure.Management.CustomerInsights](https://www.nuget.org/packages/Microsoft.Azure.Management.CustomerInsights)
[Data Factory](/azure/data-factory/) | | [Microsoft.Azure.Management.DataFactories](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)
[Data Lake Analytics](/azure/data-lake-analytics/) | [Microsoft.Azure.Management.DataLake.Analytics](http://www.nuget.org/packages/Microsoft.Azure.Management.DataLake.Analytics) | [Microsoft.Azure.Management.DataLake.Analytics](https://www.nuget.org/packages/Microsoft.Azure.Management.DataLake.Analytics)
[Data Lake Store](/azure/data-lake-store/) | [Microsoft.Azure.Management.DataLake.Store](http://www.nuget.org/packages/Microsoft.Azure.Management.DataLake.Store) | [Microsoft.Azure.Management.DataLake.Store](https://www.nuget.org/packages/Microsoft.Azure.Management.DataLake.Store)<br/>[Microsoft.Azure.Management.DataLake.StoreUploader](https://www.nuget.org/packages/Microsoft.Azure.Management.DataLake.StoreUploader)
[开发测试实验室](/azure/devtest-lab/) | | [Microsoft.Azure.Management.DevTestLabs](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs)
[DNS](/azure/dns/) | | [Microsoft.Azure.Management.Dns.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Dns.Fluent)<br/>[Microsoft.Azure.Management.Dns](https://www.nuget.org/packages/Microsoft.Azure.Management.Dns)
[DocumentDB](/azure/documentdb/) | [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core) | 
[事件中心](/azure/event-hubs/) | [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs)<br/>[Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor)
[Graph](/rest/api/graphrbac) | | [Microsoft.Azure.Graph.RBAC](https://www.nuget.org/packages/Microsoft.Azure.Graph.RBAC)
[HD Insight](/azure/hdinsight/) | [Microsoft.Azure.Management.HDInsight.Job](http://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight.Job) | [Microsoft.Azure.Management.HDInsight](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight)
[IoT 中心](/azure/iot-hub) | [Microsoft.Azure.Devices](https://www.nuget.org/packages/Microsoft.Azure.Devices)&nbsp;<b>&#42;</b><br/>[Microsoft.Azure.Devices.Client](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client)&nbsp;<b>&#42;</b>
[密钥保管库](/azure/key-vault/) | [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) | [Microsoft.Azure.Management.KeyVault.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.KeyVault.Fluent)<br/>[Microsoft.Azure.Management.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.Management.KeyVault)
[Log Analytics](/azure/log-analytics/) | | [Microsoft.Azure.Management.OperationalInsights](https://www.nuget.org/packages/Microsoft.Azure.Management.OperationalInsights)
[逻辑应用](/azure/logic-apps/) | | [Microsoft.Azure.Management.Logic](https://www.nuget.org/packages/Microsoft.Azure.Management.Logic)
[机器学习](/azure/machine-learning/) | | [Microsoft.Azure.Management.MachineLearning](https://www.nuget.org/packages/Microsoft.Azure.Management.MachineLearning)
[媒体服务](/azure/media-services/) | [windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions)&nbsp;<b>&#42;</b> | [Microsoft.Azure.Management.Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media)
[监视](/azure/monitoring-and-diagnostics/) | | [Microsoft.Azure.Insights](https://www.nuget.org/packages/Microsoft.Azure.Insights)
[通知中心](/azure/notification-hubs/) | [Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs)&nbsp;<b>&#42;</b><br/>[WindowsAzure.Messaging.Managed](https://www.nuget.org/packages/WindowsAzure.Messaging.Managed)&nbsp;<b>&#42;</b> | [Microsoft.Azure.Management.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.Management.NotificationHubs)
[PowerBI Embedded](/azure/power-bi-embedded/) | | [Microsoft.Azure.Management.PowerBIEmbedded](https://www.nuget.org/packages/Microsoft.Azure.Management.PowerBIEmbedded)
[恢复服务](/azure/site-recovery/) | | [Microsoft.Azure.Management.RecoveryServices](https://www.nuget.org/packages/Microsoft.Azure.Management.RecoveryServices)
[Redis 缓存](/azure/redis-cache/) | [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) | [Microsoft.Azure.Management.Redis.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Redis.Fluent)<br/>[Microsoft.Azure.Management.Redis](https://www.nuget.org/packages/Microsoft.Azure.Management.Redis)
[资源管理器](/azure/azure-resource-manager/) | | [Microsoft.Azure.Management.ResourceManager.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager.Fluent)<br/>[Microsoft.Azure.Management.ResourceManager](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)
[计划程序](/azure/scheduler/) | | [Microsoft.Azure.Management.Scheduler](https://www.nuget.org/packages/Microsoft.Azure.Management.Scheduler)
[搜索](/azure/search/) | [Microsoft.Azure.Search](https://www.nuget.org/packages/Microsoft.Azure.Search)&nbsp;<b>&#42;</b> | [Microsoft.Azure.Management.Search](https://www.nuget.org/packages/Microsoft.Azure.Management.Search)
[服务总线](/azure/service-bus/) | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus)&nbsp;<b>&#42;</b> | [Microsoft.Azure.Management.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.Management.ServiceBus)
[服务总线中继](/azure/service-bus-relay/) | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay)
[Service Fabric](/azure/service-fabric/) | [Microsoft.ServiceFabric](https://www.nuget.org/profiles/servicefabric)&nbsp;<b>&#42;</b> | 
[SQL 数据库](/azure/sql-database/) | [System.Data.SqlClient](https://www.nuget.org/packages/System.Data.SqlClient) | [Microsoft.Azure.Management.Sql.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql.Fluent)<br/>[Microsoft.Azure.Management.Sql](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)
[存储](/azure/storage/) | [WindowsAzure.Storage](http://www.nuget.org/packages/WindowsAzure.Storage) | [Microsoft.Azure.Management.Storage.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage.Fluent)<br/>[Microsoft.Azure.Management.Storage](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage)<br/>[Microsoft.Azure.Storage.DataMovement](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement)
[流分析](/azure/stream-analytics/) | | [Microsoft.Azure.Management.StreamAnalytics](https://www.nuget.org/packages/Microsoft.Azure.Management.StreamAnalytics)
[流量管理器](/azure/traffic-manager/) | | [Microsoft.Azure.Management.TrafficManager.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.TrafficManager.Fluent)<br/>[Microsoft.Azure.Management.TrafficManager](https://www.nuget.org/packages/Microsoft.Azure.Management.TrafficManager)
[虚拟机](/azure/virtual-machines/) | | [Microsoft.Azure.Management.Compute.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent)<br/>[Microsoft.Azure.Management.Compute](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute)
[虚拟网络](/azure/virtual-network/) | | [Microsoft.Azure.Management.Network.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Network.Fluent)<br/>[Microsoft.Azure.Management.Network](https://www.nuget.org/packages/Microsoft.Azure.Management.Network)
[Web 应用](/azure/app-service-web) | | [Microsoft.Azure.Management.AppService.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.AppService.Fluent)<br/>[Microsoft.Azure.Management.Websites](https://www.nuget.org/packages/Microsoft.Azure.Management.Websites)

\* *- 表示包当前与 .NET Core 不兼容。*

## <a name="installation"></a>安装

### <a name="visual-studio"></a>Visual Studio

如果使用 Visual Studio，请使用 **NuGet 包管理器控制台**将包导入到项目中。

1. Visual Studio 解决方案打开后，通过依次单击“工具”、“NuGet 程序包管理器”、“程序包管理器控制台”启动该控制台。  

    ![程序包管理器控制台](media/index/package-manager.png)

2. 在控制台窗口中，使用 **Install-Package** cmdlet 来下载并安装 NuGet 包。  例如，若要包括用于 .NET 的 [Azure 资源管理库](http://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)最新版：

    ```powershell
    Install-Package Microsoft.Azure.Management.ResourceManager -Pre 
    ``` 
    若要使用特定版本，请包括版本号，如下所示：

    ```powershell
    Install-Package Microsoft.Azure.Management.ResourceMananger -Version 1.4.0-preview
    ``` 

### <a name="other-editors"></a>其他编辑器

如果将 .NET Core 与 Visual Studio Code（或任何其他编辑器）配合使用，请编辑 csproj 文件，将包添加为 **PackageReference** 元素。  此示例使用 **Microsoft.Azure.Management.ResourceManager** 的特定版，这是最佳做法，但 NuGet [可变版本](/nuget/consume-packages/package-references-in-project-files#floating-versions)也受支持。

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>netcoreapp1.1</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.Azure.Management.ResourceManager" Version="1.4.0-preview" />
  </ItemGroup>

</Project>
```

> [!TIP]
> 以前版本的 .NET Core 使用 project.json 文件而不是 csproj。  若要了解如何将 project.json 映射到 csproj，[请参阅此文档](/dotnet/articles/core/tools/project-json-to-csproj)。

