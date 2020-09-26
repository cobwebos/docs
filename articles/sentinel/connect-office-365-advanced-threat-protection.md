---
title: 将 Microsoft Defender for Office 365 (以前的 Office 365 ATP) 数据连接到 Azure Sentinel |Microsoft Docs
description: 将 Microsoft Defender for Office 365 数据引入 Azure Sentinel，以获得可见性并生成自动响应方案。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: yelevin
ms.openlocfilehash: 5ac72c9861e06773ffab87dcbf4e730562b798fe
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91341587"
---
# <a name="connect-alerts-from-microsoft-defender-for-office-365"></a>连接来自 Microsoft Defender for Office 365 的警报 

> [!IMPORTANT]
>
> - **Microsoft Defender For office 365** 以前称为 **Office 365 高级威胁防护 (ATP) **。
>
>     你可能会看到产品中仍在使用旧名称 (将其数据连接器包含在 Azure Sentinel) 中一段时间。
>
> - 引入 Microsoft Defender for Office 365 警报当前提供公共预览版。 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
 
[Microsoft Defender For Office 365](https://docs.microsoft.com/office365/servicedescriptions/office-365-advanced-threat-protection-service-description) 针对零天和其他高级威胁保护你的组织，使其免受电子邮件、恶意 URL 链接和协作工具中的未知恶意软件的侵害。 通过将 Microsoft Defender for Office 365 警报引入到 Azure Sentinel，你将能够在安全操作中利用有关电子邮件、文件共享和基于 URL 的威胁的信息。 然后，你可以更全面地分析组织中的安全事件，并构建行动手册以实现有效且即时的响应。

连接器将导入以下警报：

- 检测到可能的恶意 URL 单击 

- 电子邮件，其中包含在传递后删除的恶意软件

- 包含在传递后删除的诈骗 Url 的电子邮件 

- 用户报告为恶意软件或网络钓鱼电子邮件 

- 检测到可疑电子邮件发送模式 

- 用户已被限制发送电子邮件 

Office **安全和合规中心**中的 office 客户可以查看这些警报。

## <a name="prerequisites"></a>先决条件

- 启用连接器时，必须对 Azure Sentinel 工作区具有读取和写入权限。

- 你必须是 Azure Sentinel 工作区租户上的全局管理员或安全管理员。

- 你必须拥有 office [365 ATP 计划 2](https://docs.microsoft.com/microsoft-365/security/office-365-security/office-365-atp#office-365-atp-plan-1-and-plan-2) (的有效许可证，其中包含 Office 365 E5、Office 365 A5 和 Microsoft 365 E5 许可证，并可单独) 购买。 

## <a name="connect-to-microsoft-defender-for-office-365"></a>连接到 Microsoft Defender for Office 365

如果部署了 Microsoft Defender for Office 365，并且配置了策略，则可以轻松地将警报引入到 Azure Sentinel。

1. 在 Azure Sentinel 中，从导航菜单中选择 " **数据连接器** "。

1. 选择 " **Microsoft Defender For Office 365** (仍称为 *Office 365 高级威胁防护* ") 在 "连接器" 库中，然后选择 " **打开连接器" 页面**。

1. 在 " **配置** " 部分中，单击 " **连接**"。 

1. 在 " **创建事件** " 部分中，单击 " **启用**"。

1. 若要使用相关架构查询 Office 365 ATP 警报，请搜索 **SecurityAlert** 并指定 **提供程序名称** 为 **OATP**。

1. 选择 " **后续步骤** " 选项卡以查看并使用与 Microsoft Defender for Office 365 连接器捆绑的查询示例和分析规则模板。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Microsoft Defender for Office 365 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。
