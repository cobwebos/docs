---
title: 监视 Azure 云服务 | Microsoft Docs
description: 介绍监视 Azure 云服务需要涉及到哪些操作，以及可以选择哪些选项。
services: cloud-services
documentationcenter: ''
author: thraka
manager: timlt
editor: ''
ms.assetid: ''
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: adegeo
ms.openlocfilehash: f3a3a1beb8540ee8ab0502379396c06ea505fb44
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2018
ms.locfileid: "29149897"
---
# <a name="introduction-to-cloud-service-monitoring"></a>云服务监视简介

可以监视任何云服务的关键性能指标。 每个云服务角色收集极少量的数据：CPU 使用率、网络使用率和磁盘利用率。 如果云服务已将 `Microsoft.Azure.Diagnostics` 扩展应用到某个角色，则该角色可以收集其他数据点。 本文介绍适用于云服务的 Azure 诊断。

使用基本监视时，默认情况下，将每隔 3 分钟从角色实例中采样和收集性能计数器数据。 此基本监视数据不会存储在存储帐户中，并且不会产生相关的额外费用。

使用高级监视时，将每隔 5 分钟、1 小时和 12 小时采样和收集其他指标。 聚合的数据存储在存储帐户的表中，将在 10 天后予以清除。 使用的存储帐户是按角色配置的；可以对不同的角色使用不同的存储帐户。 这种配置是使用 [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) 和 [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) 文件中的连接字符串完成的。


## <a name="basic-monitoring"></a>基本监视

如简介中所述，云服务自动从虚拟机主机收集基本监视数据。 此数据包括 CPU 百分比、网络输入/输出和磁盘读取/写入。 Azure 门户中云服务的概述和指标页上自动显示了所收集的监视数据。 

基本监视不需要存储帐户。 

![基本云服务监视磁贴](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>高级监视

高级监视涉及到对想要监视的角色使用 **Azure 诊断**扩展（和可选的 Application Insights SDK）。 该诊断扩展使用名为 **diagnostics.wadcfgx** 的配置文件（按角色）来配置所监视的诊断指标。 Azure 诊断扩展收集数据，并将数据存储在 Azure 存储帐户中。 在 **.wadcfgx**、[.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) 和 [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) 文件中配置这些设置。 这意味着，高级监视会产生额外的成本。

创建每个角色时，Visual Studio 会将 Azure 诊断扩展添加到其中。 此诊断扩展可收集以下类型的信息：

* 自定义性能计数器
* 应用程序日志
* Windows 事件日志
* .NET 事件源
* IIS 日志
* 基于清单的 ETW
* 故障转储
* 客户错误日志

> [!IMPORTANT]
> 尽管所有这些数据都会聚合到存储帐户中，但门户**不**提供绘制数据图表的本机方法。 强烈建议你将另一个服务（如 Application Insights）集成到应用程序中。

## <a name="setup-diagnostics-extension"></a>设置诊断扩展

首先，如果你没有**经典**存储帐户，请[创建一个](../storage/common/storage-create-storage-account.md#create-a-storage-account)。 确保为创建的存储帐户指定**经典部署模型**。

接下来，导航到“存储帐户(经典)”资源。 选择“设置” > “访问密钥”，并复制“主连接字符串”值。 云服务需要此值。 

必须更改两个配置文件才能启用高级诊断：**ServiceDefinition.csdef** 和 **ServiceConfiguration.cscfg**。

### <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef

在 **ServiceDefinition.csdef** 文件中，针对使用高级诊断的每个角色添加名为 `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` 的新设置。 创建新项目时，Visual Studio 会将此值添加到文件。 如果缺少该设置，可以立即添加。 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

此代码定义必须添加到每个 **ServiceConfiguration.cscfg** 文件的新设置。 

很可能有两个 **.cscfg** 文件，一个名为 **ServiceConfiguration.cloud.cscfg**，用于部署到 Azure；另一个名为 **ServiceConfiguration.local.cscfg**，用于在模拟环境中进行本地部署。 打开并更改每个 **.cscfg** 文件。 添加名为 `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` 的设置。 将值设置为经典存储帐户的**主连接字符串**。 如果想要在开发计算机上使用本地存储，请使用 `UseDevelopmentStorage=true`。

```xml
<ServiceConfiguration serviceName="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="WorkerRoleWithSBQueue1">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=mystorage;AccountKey=KWwkdfmskOIS240jnBOeeXVGHT9QgKS4kIQ3wWVKzOYkfjdsjfkjdsaf+sddfwwfw+sdffsdafda/w==" />
      
      <!-- or use the local development machine for storage
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
      -->
```

## <a name="use-application-insights"></a>使用 Application Insights

从 Visual Studio 发布云服务时，可以选择将诊断数据发送到 Application Insights。 可以在那时创建 Application Insights Azure 资源，或者将数据发送到现有 Azure 资源。 Application Insights 可以监视云服务的可用性、性能、故障和使用情况。 可将自定义图表添加到 Application Insights，以便查看最重要的数据。 在云服务项目中使用 Application Insights SDK 可以收集角色实例数据。 有关如何集成 Application Insights 的详细信息，请参阅[包含云服务的 Application Insights](../application-insights/app-insights-cloudservices.md)。

请注意，尽管可以使用 Application Insights 来显示通过 Windows Azure 诊断扩展指定的性能计数器（和其他设置），但是只有将 Application Insights SDK 集成到辅助角色和 Web 角色，才能获得更丰富的体验。


## <a name="next-steps"></a>后续步骤

- [了解包含云服务的 Application Insights](../application-insights/app-insights-cloudservices.md)
- [设置性能计数器](diagnostics-performance-counters.md)

