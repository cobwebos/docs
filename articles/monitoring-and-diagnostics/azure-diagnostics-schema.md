---
title: "Azure 诊断扩展配置架构版本和历史记录 | Microsoft Docs"
description: "有关收集 Azure 虚拟机、VM 规模集、Service Fabric 和云服务中的性能计数器的信息。"
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/16/2017
ms.author: robb
ms.openlocfilehash: 119e8a237f24cdc80a1ab8e376f2b308c9eada05
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="azure-diagnostics-extention-configuration-schema-versions-and-history"></a>Azure 诊断扩展配置架构版本和历史记录
此页为 Microsoft Azure SDK 附带的 Azure 诊断扩展架构版本编制了索引。  

> [!NOTE]
> Azure 诊断扩展是用来从以下各项收集性能计数器和其他统计信息的组件：
> - Azure 虚拟机 
> - 虚拟机规模集
> - Service Fabric 
> - 云服务 
> - 网络安全组
> 
> 在使用以下某个服务时，才需要参阅此页。

Azure 诊断扩展可以与其他 Microsoft 诊断产品（例如 Azure Monitor、Application Insights 和 Log Analytics）结合使用。 有关详细信息，请参阅 [Microsoft 监视工具概述](monitoring-overview.md)。

## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Azure SDK 和诊断版本随附图  

|Azure SDK 版本 | 诊断扩展版本 | 模型|  
|------------------|-------------------------------|------|  
|1.x               |1.0                            |插件|  
|2.0 - 2.4         |1.0                            |插件|  
|2.5               |1.2                            |扩展|  
|2.6               |1.3                            |"|  
|2.7               |1.4                            |"|  
|2.8               |1.5                            |"|  
|2.9               |1.6                            |"|
|2.96              |1.7                            |"|
|2.96              |1.8                            |"|
|2.96              |1.8.1                          |"|
|2.96              |1.9                            |"|



 Azure 诊断 1.0 版本最初随附于插件模型中，也就是说，安装 Azure SDK 时，便可获取附带的 Azure 诊断版本。  

 从 SDK 2.5（诊断版本 1.2）开始，Azure 诊断随附于扩展模型中。 使用新功能的工具仅在较新版本的 Azure SDK 中可用，但使用 Azure 诊断的任何服务都将直接从 Azure 选取最新的附带版本。 例如，仍在使用 SDK 2.5 的任何用户在使用较新功能时都将加载上表中显示的最新版本。  

## <a name="schemas-index"></a>架构索引  
不同版本的 Azure 诊断使用不同的配置架构。 

[诊断 1.0 配置架构](azure-diagnostics-schema-1dot0.md)  

[诊断 1.2 配置架构](azure-diagnostics-schema-1dot2.md)  

[诊断 1.3 及更高版本的配置架构](azure-diagnostics-schema-1dot3-and-later.md)  

## <a name="version-history"></a>版本历史记录


### <a name="diagnostics-extension-19"></a>诊断扩展 1.9 
添加了 Docker 支持。


### <a name="diagnostics-extension-181"></a>诊断扩展 1.8.1 
可以在专用配置中指定 SAS 令牌而非存储帐户密钥。 如果提供了 SAS 令牌，则会忽略存储帐户密钥。


```json
{
    "storageAccountName": "diagstorageaccount",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    }
}
```

```xml
<PrivateConfig>
    <StorageAccount name="diagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    <SecondaryStorageAccounts>
        <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>
</PrivateConfig>
```


### <a name="diagnostics-extension-18"></a>诊断扩展 1.8 
向 PublicConfig 中添加了 StorageType。 StorageType 可以是 *Table*、*Blob* 或 *TableAndBlob*。 *Table* 是默认值。


```json
{
    "WadCfg": {
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

```xml
<PublicConfig>
    <WadCfg />
    <StorageAccount>diagstorageaccount</StorageAccount>
    <StorageType>TableAndBlob</StorageType>
</PublicConfig>
```


### <a name="diagnostics-extension-17"></a>诊断扩展 1.7 
添加了路由到 EventHub 的功能。

### <a name="diagnostics-extension-15"></a>诊断扩展 1.5
添加了 sinks 元素和将诊断数据发送到 [Application Insights](../application-insights/app-insights-cloudservices.md) 的功能，因此可以更轻松地在应用程序以及系统和基础结构级别诊断问题。

### <a name="azure-sdk-26-and-diagnostics-extension-13"></a>Azure SDK 2.6 和诊断扩展 1.3 
对 Visual Studio 中的云服务项目进行了以下更改。 （这些更改同样适用于更高版本的 Azure SDK。）

* 本地模拟器现在支持诊断。 这意味着，在 Visual Studio 中开发和测试时，可以收集诊断数据并确保应用程序正在创建相应的跟踪。 使用 Azure 存储模拟器在 Visual Studio 中运行云服务项目时，连接字符串 `UseDevelopmentStorage=true` 可启用诊断数据收集。 所有诊断数据都在“(开发存储)”存储帐户中收集。
* 诊断存储帐户连接字符串 (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) 将再次存储在服务配置 (.cscfg) 文件中。 在 Azure SDK 2.5 中，在 diagnostics.wadcfgx 文件中指定诊断存储帐户。

连接字符串在 Azure SDK 2.4 及更早版本中的工作方式与它在 Azure SDK 2.6 中的工作方式之间有一些明显的差异。

* 在 Azure SDK 2.4 及更早版本中，连接字符串由诊断插件用作运行时以获取用于传输诊断日志的存储帐户信息。
* 在 Azure SDK 2.6 中，Visual Studio 在发布过程中通过诊断连接字符串使用相应的存储帐户信息配置诊断扩展。 连接字符串让你为 Visual Studio 会在发布时使用的不同服务配置定义不同的存储帐户。 但是，因为诊断插件已不再可用（在 Azure SDK 2.5 之后），.cscfg 文件本身不能启用诊断扩展。 必须通过工具（如 Visual Studio 或 PowerShell）单独启用扩展。
* 为了简化使用 PowerShell 配置诊断扩展的过程，Visual Studio 的程序包输出还包含每个角色的诊断扩展的公共配置 XML。 Visual Studio 使用诊断连接字符串来填充公共配置中存在的存储帐户信息。 公共配置文件在“扩展”文件夹中创建，并遵循模式 PaaSDiagnostics<RoleName>.PubConfig.xml。 任何基于 PowerShell 的部署都可以使用此模式将每个配置映射到角色。
* .cscfg 文件中的连接字符串还由 Azure 门户用于访问诊断数据，使这些数据可以显示在“监视”选项卡中。 需要连接字符串才能配置服务以在门户中显示详细监视数据。

#### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>将项目迁移到 Azure SDK 2.6 和更高版本
从 Azure SDK 2.5 迁移到 Azure SDK 2.6 或更高版本时，如果在 .wadcfgx 文件中指定了诊断存储帐户，则该帐户将继续保留在那里。 要针对不同存储配置充分使用不同存储帐户的灵活性，必须手动将连接字符串添加到项目。 如果将项目从 Azure SDK 2.4 或更低版本迁移到 Azure SDK 2.6，系统将保留诊断连接字符串。 但是，请注意 Azure SDK 2.6 中处理连接字符串的方式的更改，如上一部分中所述。

#### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Visual Studio 如何确定诊断存储帐户
* 如果在 .cscfg 文件中指定了诊断连接字符串，则 Visual Studio 在发布时以及在打包过程中生成公共配置 xml 文件时会使用它来配置诊断扩展。
* 如果未在 .cscfg 文件中指定任何诊断连接字符串，则 Visual Studio 在发布时以及在打包过程中生成公共配置 xml 文件时会回退到使用 .wadcfgx 文件中指定的存储帐户来配置诊断扩展。
* .cscfg 文件中的诊断连接字符串将优先于 .wadcfgx 文件中的存储帐户。 如果在 .cscfg 文件中指定了诊断连接字符串，则 Visual Studio 将使用该字符串，而忽略 .wadcfgx 中的存储帐户。

#### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>“更新开发存储连接字符串...”复选框的作用 是什么？
“在发布到 Microsoft Azure 时使用 Microsoft Azure 存储帐户凭据更新诊断和缓存的开发存储连接字符串”复选框提供了使用发布过程中指定的 Azure 存储帐户更新任何开发存储帐户连接字符串的简便方法。

例如，假设你选中此复选框，并且诊断连接字符串指定 `UseDevelopmentStorage=true`。 将项目发布到 Azure 时，Visual Studio 会自动使用发布向导中指定的存储帐户更新诊断连接字符串。 但是，如果已将实际的存储帐户指定为诊断连接字符串，则将改用该帐户。

### <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Azure SDK 2.4 及更早版本与 Azure SDK 2.5 及更高版本之间的诊断功能差异
如果要将项目从 Azure SDK 2.4 升级到 Azure SDK 2.5 或更高版本，则应考虑到以下诊断功能差异。

* **配置 API 已弃用** – 诊断的编程配置在 Azure SDK 2.4 或更早版本中可用，但在 Azure SDK 2.5 及更高版本中已弃用。 如果目前在代码中定义了诊断配置，则需在已迁移的项目中从头开始重新配置这些设置，这样才能让诊断正常工作。 Azure SDK 2.4 的诊断配置文件是 diagnostics.wadcfg，而 Azure SDK 2.5 及更高版本的诊断配置文件是 diagnostics.wadcfgx。
* **云服务应用程序的诊断只能在角色级别配置，而不能在实例级别配置。**
* **每次部署应用程序时，都会更新诊断配置** – 如果从服务器资源管理器更改诊断配置并重新部署应用，这可能会导致奇偶校验问题。
* **在 Azure SDK 2.5 及更高版本中，故障转储是在诊断配置文件而非代码中配置的** – 如果在代码中配置了故障转储，则必须手动将配置从代码传输至配置文件，因为故障转储并未在迁移至 Azure SDK 2.6 的过程中传输。

