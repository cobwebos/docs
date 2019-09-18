---
title: 将 Azure 信息保护数据连接到 Azure Sentinel Preview |Microsoft Docs
description: 了解如何在 Azure Sentinel 中连接 Azure 信息保护数据。
services: sentinel
documentationcenter: na
author: cabailey
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2019
ms.author: cabailey
ms.openlocfilehash: 0614d24b19ef39cebdf4cb47fdd2d44470ea59c0
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067741"
---
# <a name="connect-data-from-azure-information-protection"></a>连接 Azure 信息保护中的数据

> [!IMPORTANT]
> Azure Sentinel 当前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

可以通过配置 Azure 信息保护数据连接器，将[Azure 信息保护](https://azure.microsoft.com/services/information-protection/)中的日志记录信息传输到 azure Sentinel。 Azure 信息保护可帮助你控制和保护敏感数据，无论数据存储在云中还是本地。

如果已配置[Azure 信息保护的集中式报告](https://docs.microsoft.com/azure/information-protection/reports-aip)，以便将此服务中的日志记录信息存储在与当前为 Azure Sentinel 选择的相同的 Log Analytics 工作区中，则可以跳过配置此数据连接器。 Azure 信息保护中的日志记录信息已可用于 Azure Sentinel。

但是，如果 Azure 信息保护中的日志记录信息将与当前为 Azure Sentinel 选择的工作区不同 Log Analytics 工作区，请执行以下操作之一：

- 更改在 Azure Sentinel 中选择的工作区。

- 更改 Azure 信息保护的工作区，可以通过配置此数据连接器来执行此操作。
    
    如果更改了工作区，则 Azure 信息保护的新报表数据现在将存储在用于 Azure Sentinel 的工作区中，而不能将历史数据提供给 Azure Sentinel 使用。 此外，如果为自定义查询、警报或 REST Api 配置了上一个工作区，则必须为 Azure Sentinel 工作区重新配置这些内容，前提是要使用它们进行 Azure 信息保护。 使用 Azure 信息保护的客户端和服务不需要重新配置。

## <a name="prerequisites"></a>先决条件

- 适用于你的租户的以下 Azure AD 管理员角色之一：Azure 信息保护管理员、安全管理员或全局管理员。
    
    > [!NOTE]
    > 如果你的租户位于[统一的标签平台](https://docs.microsoft.com/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)上，则不能使用 Azure 信息保护管理员角色。

- 读取和写入用于 Sentinel 和 Azure 信息保护的 Log Analytics 工作区的权限。

- Azure 信息保护已添加到 Azure 门户。 如果需要此步骤的帮助，请参阅[将 Azure 信息保护添加到 Azure 门户](https://docs.microsoft.com/azure/information-protection/quickstart-viewpolicy#add-azure-information-protection-to-the-azure-portal)。

## <a name="connect-to-azure-information-protection"></a>连接到 Azure 信息保护

如果尚未为 Azure 信息保护配置 Log Analytics 工作区，或者需要更改存储 Azure 信息保护日志记录信息的工作区，请使用以下说明。 

1. 在 Azure Sentinel 中，选择 "**数据连接器**"，然后选择 " **Azure 信息保护**"。

2. 在 " **Azure 信息保护**" 边栏选项卡中，选择 "**打开连接器" 页**。

3. 在下一个边栏选项卡上的 "**配置**" 部分中，选择 " **azure 信息保护**" 以前往**azure 信息保护分析**。

4. 从可用工作区列表中，选择当前用于 Azure Sentinel 的工作区。 如果选择其他工作区，azure 信息保护中的报表数据将不可用于 Azure Sentinel。

5. 选择工作区后，选择 **"确定"** ，此时连接器**状态**应更改为 "**已连接**"。

6. Azure 信息保护中的报表数据存储在所选工作区中的**InformationProtectionLogs_CL**表中。 
    
    若要为此报表数据使用 Azure Monitor 中的相关架构，请搜索**InformationProtectionEvents**。 有关这些事件函数的信息，请参阅 Azure 信息保护文档中的[事件函数的友好架构参考](https://docs.microsoft.com/azure/information-protection/reports-aip#friendly-schema-reference-for-event-functions)部分。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Azure 信息保护连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何了解[你的数据以及潜在的威胁](quickstart-get-visibility.md)。
- 开始[通过 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。
