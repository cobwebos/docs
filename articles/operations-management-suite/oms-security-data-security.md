---
title: "Operations Management Suite 安全和审核解决方案数据安全 | Microsoft Docs"
description: "本文档介绍 Operations Management Suite 安全和审核解决方案中如何管理和保护数据。"
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 9cdf7deb-2a30-4672-b89f-71179ee8326a
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: yurid
ms.openlocfilehash: 3b6327b1f5150f32afd71639f32c55d823f1d1f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="operations-management-suite-security-and-audit-solution-data-security"></a>Operations Management Suite 安全和审核解决方案数据安全
为帮助客户防范、检测和应对威胁，[Operations Management Suite (OMS) 安全和审核解决方案](operations-management-suite-overview.md)将收集并处理关于资源的数据，其中包括：

* 安全事件日志
* Windows 事件跟踪 (ETW) 事件
* AppLocker 审核事件
* Windows 防火墙日志
* 高级威胁分析事件
* 基线评估的结果
* 反恶意软件评估的结果
* 更新/修补程序评估的结果
* 代理上显式启用的 syslog 流

我们强力承诺保护此类数据的隐私和安全性。 从服务的编码到运营，Microsoft 都严格遵守相关法规与安全准则。
本文介绍 OMS 安全和审核解决方案中如何管理和保护数据。

## <a name="data-sources"></a>数据源
OMS 安全和审核解决方案分析来自已安装 OMS 代理的虚拟机和物理计算机的数据。 OMS 安全和审核解决方案可以收集有关安全事件（例如 Windows 事件、审核日志、IIS 日志和 syslog 消息）的配置信息。 此类数据的示例包括：操作系统类型和版本、正在运行的进程、计算机名称、IP 地址、已登录用户、租户 ID。  

## <a name="data-protection"></a>数据保护
**数据隔离**：服务的每个组件的数据都保持逻辑隔离。 所有数据均按组织进行标记。 此标记方式贯穿数据的整个生命周期，在服务的每个层强制实施。 

**数据访问**：为了提供安全建议和调查潜在的安全威胁，Microsoft 人员可能访问该服务收集或分析的信息。 我们遵守 [Microsoft Online Services 条款](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)和[隐私声明](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx)，这些条款和声明指出，Microsoft 不会将客户数据或其衍生信息用于任何广告目的或类似的商业目的。 为了提供安全建议和调查潜在的安全威胁，Microsoft 人员可能访问该服务收集或分析的信息。 我们只会根据需要将客户数据用于向用户提供 Azure 服务，包括用于与提供这些服务相对应的目的。 用户保留对其数据的所有权利。

**数据使用**：Microsoft 使用多个租户所使用的模式和威胁情报增强用户预防和检测威胁的能力；执行过程中遵循[隐私声明](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx)中所述的隐私承诺。

> [!NOTE]
> 创建工作区是初始 OMS 安全和审核配置过程的一部分，其时在 OMS 工作区级别配置数据位置。
> 
> 

## <a name="see-also"></a>另请参阅
本文档介绍 OMS 中如何管理和保护数据。 若要了解有关 OMS 安全和审核解决方案的详细信息，请参阅：

* [Operations Management Suite (OMS) 概述](operations-management-suite-overview.md)
* [监视和响应 Operations Management Suite 安全和审核解决方案中的安全警报](oms-security-responding-alerts.md)
* [监视 Operations Management Suite 安全和审核解决方案中的资源](oms-security-monitoring-resources.md)

