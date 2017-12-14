---
title: "Azure 安全中心平台迁移 | Microsoft Docs"
description: "本文档介绍了对 Azure 安全中心数据收集方式的一些更改。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 80246b00-bdb8-4bbc-af54-06b7d12acf58
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: yurid
ms.openlocfilehash: 89970b50a2f7246a43ac9666be4d992649605cbf
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2017
---
# <a name="azure-security-center-platform-migration"></a>Azure 安全中心平台迁移

从 2017 年 6 月初起，Azure 安全中心推出对安全数据收集和存储方式的重要更改。  这些更改解锁了新功能，例如可以轻松搜索安全数据并更好地与其他 Azure 管理和监视服务保持一致。

> [!NOTE]
> 平台迁移不应影响生产资源，也无需采取任何操作。


## <a name="whats-happening-during-this-platform-migration"></a>平台迁移过程中发生了什么？

以前，安全中心使用 Azure Monitoring Agent从 VM 中收集安全数据。 这包括安全配置（用于识别漏洞）和安全事件（用于检测威胁）相关信息。 此数据存储在 Azure 存储帐户中。

此后，安全中心使用 Microsoft Monitoring Agent - Operations Management Suite 和 Log Analytics 服务同样使用此代理。 通过此代理收集的数据存储在与 Azure 订阅关联的现有 Log Analytics [工作区](../log-analytics/log-analytics-manage-access.md)或新工作区中，具体取决于 VM 的地理位置。

## <a name="agent"></a>代理

转换过程中，Microsoft Monitoring Agent（适用于 [Windows](../log-analytics/log-analytics-windows-agent.md) 或 [Linux](../log-analytics/log-analytics-linux-agents.md)）安装于当前正收集数据的所有 Azure VM 上。  如果 VM 已安装 Microsoft Monitoring Agent，安全中心将利用当前安装的代理。

一段时间（通常为几天）内，这两个代理将并行运行，确保数据顺利转换且无任何数据丢失。 这样 Microsoft 就可以在终止使用当前管道之前，验证新数据管道是否可用。 验证后，系统会从 VM 中删除 Azure Monitoring Agent。 无需用户执行任何操作。 迁移所有客户后，你将收到电子邮件通知。
 
不建议在迁移期间手动卸载 Azure Monitoring Agent，因为可能导致安全数据差距。 如果需要进一步帮助，请咨询 [Microsoft 客户服务和支持](https://support.microsoft.com/contactus/)。 

适用于 Windows 的 Microsoft Monitoring Agent 需要使用 TCP 端口 443，有关详细信息，请参阅 [Azure 安全中心故障排除指南](security-center-troubleshooting-guide.md)。


> [!NOTE] 
> 由于其他 Azure 管理和监视服务可能使用 Microsoft Monitoring Agent，因此关闭安全中心数据收集后不会自动卸载代理。 但是，必要时可手动卸载代理。

## <a name="workspace"></a>工作区

如上所述，通过 Microsoft Monitoring Agent（代表安全中心）收集的数据存储在与 Azure 订阅关联的现有 Log Analytics 工作区或新工作区中，具体取决于 VM 的地理位置。

在 Azure 门户中，可浏览查看 Log Analytics 工作区的列表，其中包括安全中心创建的任何工作区。 系统会为新工作区创建相关资源组。 二者均遵循此命名约定：

- 工作区：DefaultWorkspace-[subscription-ID]-[geo]
- 资源组：DefaultResouceGroup-[geo] 
 
对于安全中心创建的工作区，数据将保留 30 天。 对于现有工作区，保留期取决于工作区定价层。

> [!NOTE]
> 安全中心以前收集的数据保留在存储帐户中。 完成迁移后，可以删除这些存储帐户。

### <a name="oms-security-solution"></a>OMS 安全解决方案 

对于未安装 OMS 安全解决方案的现有客户，Microsoft 将此解决方案安装在客户工作区，但仅针对 Azure VM。 请不要卸载此解决方案，因为如果从 OMS管理控制台完成此操作，则不会执行自动修正。


## <a name="other-updates"></a>其他更新

连同平台迁移，我们将推出一些其他的次要更新：

- 支持其他 OS 版本。 在[此处](security-center-faq.md#virtual-machines)查看列表。
- 扩展 OS 漏洞列表。 在[此处](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)查看列表。
- [定价](https://azure.microsoft.com/pricing/details/security-center/)按每小时（以前是按天）计算，这将节省某些客户的成本。
- 需要启用“数据收集”功能，并且系统会为标准定价层的客户自动启用此功能。
- Azure 安全中心开始发现未通过 Azure 扩展部署的反恶意软件解决方案。 首先提供关于 Symantec Endpoint Protection 和 Windows 2016 Defender 的发现。
- 防护策略和通知只能在“通知”级别配置，但定价仍可在“资源组”级别设置

