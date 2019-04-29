---
title: Azure Monitor 即将停用指标和自动缩放的经典部署模型 API
description: 指标和自动缩放经典 API（也称为 Azure 服务管理 (ASM) 或 RDFE 部署模型）即将停用
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/19/2018
ms.author: robb
ms.openlocfilehash: ce54b63aa7831ed40a8592d536c43fc83fdc5567
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60709977"
---
# <a name="azure-monitor-retirement-of-classic-deployment-model-apis-for-metrics-and-autoscale"></a>Azure Monitor 即将停用指标和自动缩放的经典部署模型 API

目前，Azure Monitor（首次发布时称为 Azure Insights）提供相应的功能用于创建和管理自动缩放设置，以及使用经典 VM 和经典云服务中的指标。 原始的基于经典部署模型的 API 集将于 **2019 年 6 月 30 日之后**，在所有区域的所有 Azure 公有云和私有云中停用。   

过去一年以来，使用一组基于 Azure 资源管理器的 API 可以实现相同的操作。 Azure 门户为自动缩放和指标使用新的 REST API。 还可以使用基于这些资源管理器 API 的新 SDK、PowerShell 和 CLI。 用于监视的合作伙伴服务使用 Azure Monitor 中基于资源管理器的新 REST API。  

## <a name="who-is-not-affected"></a>哪些用户不受影响

如果你是通过 Azure 门户、[新的 Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)、PowerShell、CLI 或资源管理器模板管理自动缩放，则不需要采取任何措施。  

如果你是通过 Azure 门户或各种[监视合作伙伴服务](../../azure-monitor/platform/partners.md)使用指标，则不需要采取任何措施。 Microsoft 正在配合监视合作伙伴迁移到新的 API。

## <a name="who-is-affected"></a>哪些用户受影响

本文适用于使用以下组件的用户：

- **经典 Azure Insights SDK** - 如果你正在使用[经典 Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/)，请改用适用于 [.NET](https://github.com/azure/azure-libraries-for-net#download) 或 [Java](https://github.com/azure/azure-libraries-for-java#download) 的新 Azure Monitor SDK。 下载 [Azure Monitor SDK NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)。

- **经典自动缩放** - 如果你正在通过定制工具或使用[经典 Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/) 调用[经典自动缩放设置 API](https://msdn.microsoft.com/library/azure/mt348562.aspx)，则应改用[资源管理器 Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/autoscalesettings)。

- **经典指标** - 如果你正在定制工具中通过[经典 REST API](https://msdn.microsoft.com/library/azure/dn510374.aspx) 或[经典 Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/) 使用指标，则应改用[资源管理器 Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/autoscalesettings)。 

如果不确定代码或定制工具是否调用经典 API，请查看以下内容：

- 查看代码或工具中引用的 URI。 经典 API 使用 URI https://management.core.windows.net。 应使用基于资源管理器的 API 的较新 URI，它以 https://management.azure.com/ 开头。

- 比较计算机上的程序集名称。 旧版经典程序集位于 https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/ 中。

- 如果使用证书身份验证来访问指标或自动缩放 API，则使用的是经典终结点和库。 新版资源管理器 API 要求通过服务主体或用户主体进行 Azure Active Directory 身份验证。

- 如果使用以下任何链接文档中引用的调用，则使用的是旧版经典 API。

  - [Windows.Azure.Management.Monitoring 类库](https://docs.microsoft.com/previous-versions/azure/dn510414(v=azure.100))

  - [监视（经典）.NET](https://docs.microsoft.com/previous-versions/azure/reference/mt348562(v%3dazure.100))

  - [IMetricOperations 接口](https://docs.microsoft.com/previous-versions/azure/reference/dn802395(v%3dazure.100))

## <a name="why-you-should-switch"></a>为何要改用新版本

自动缩放和指标的所有现有功能将继续通过新 API 工作。  

迁移到新版 API 可以获得基于资源管理器的功能，例如，在所有监视服务中支持一致的基于角色的访问控制 (RBAC)。 此外，还可以获得其他指标功能： 

- 维度支持
- 在所有服务中保持一致的 1 分钟指标粒度 
- 更方便的查询
- 更长的数据保留期（保留指标 93 天，而不是30 天） 

总体而言，与 Azure 中的其他所有服务一样，基于资源管理器的 Azure Monitor API 提供更好的性能、可伸缩性和可靠性。 

## <a name="what-happens-if-you-do-not-migrate"></a>如果不迁移会发生什么情况

### <a name="before-retirement"></a>停用之前

不会对 Azure 服务或其工作负荷造成任何直接影响。  

### <a name="after-retirement"></a>停用之后

对前面所列的经典 API 发出任何调用将会失败，并返回如下所示的错误消息：

对于自动缩放：“此 API 已弃用。请使用 Azure 门户、Azure Monitor SDK、PowerShell、CLI 或资源管理器模板管理自动缩放设置。”  

对于指标：“此 API 已弃用。请使用 Azure 门户、Azure Monitor SDK、PowerShell 或 CLI 查询指标。”

## <a name="email-notifications"></a>电子邮件通知

停用通知已发送到以下帐户角色的电子邮件地址： 

- 帐户和服务管理员
- 共同管理员  

如有任何疑问，请通过 MonitorClassicAPIhelp@microsoft.com 联系我们。  

## <a name="references"></a>参考

- [Azure Monitor 的新版 REST API](https://docs.microsoft.com/rest/api/monitor/) 
- [新版 Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)
