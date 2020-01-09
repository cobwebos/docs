---
title: 将 Azure 信息保护连接到 Azure Sentinel
description: 了解如何在 Azure Sentinel 中连接 Azure 信息保护数据。
services: sentinel
author: cabailey
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: cabailey
ms.openlocfilehash: ef97ad601436faf44b0f49bd48d78c4c9420c7c8
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563677"
---
# <a name="connect-data-from-azure-information-protection"></a>连接 Azure 信息保护中的数据

> [!IMPORTANT]
> Azure Sentinel 中的 Azure 信息保护数据连接器目前为公共预览版。
> 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

可以通过配置 Azure 信息保护数据连接器，将[Azure 信息保护](https://azure.microsoft.com/services/information-protection/)中的日志记录信息传输到 azure Sentinel。 Azure 信息保护可帮助你控制和保护敏感数据，无论数据存储在云中还是本地。

如果已配置[Azure 信息保护的集中式报告](https://docs.microsoft.com/azure/information-protection/reports-aip)，以便将此服务中的日志记录信息存储在与当前为 Azure Sentinel 选择的相同的 Log Analytics 工作区中，则可以跳过此数据连接器的配置。 Azure 信息保护中的日志记录信息已可用于 Azure Sentinel。

但是，如果 Azure 信息保护中的日志记录信息将与当前为 Azure Sentinel 选择的工作区不同 Log Analytics 工作区，请执行以下操作之一：

- 更改在 Azure Sentinel 中选择的工作区。

- 更改 Azure 信息保护的工作区，可以通过配置此数据连接器来执行此操作。
    
    如果更改了工作区，则 Azure 信息保护的新报表数据现在将存储在用于 Azure Sentinel 的工作区中，而不能将历史数据提供给 Azure Sentinel 使用。 此外，如果为自定义查询、警报或 REST Api 配置了上一个工作区，则必须为 Azure Sentinel 工作区重新配置这些内容，前提是要使用它们进行 Azure 信息保护。 使用 Azure 信息保护的客户端和服务不需要重新配置。

## <a name="prerequisites"></a>必备组件

- 适用于你的租户的以下 Azure AD 管理员角色之一： 
    - Azure 信息保护管理员
    - 安全管理员
    - 法规管理员
    - 相容性数据管理员
    - 全局管理员
    
    > [!NOTE]
    > 如果你的租户位于[统一的标签平台](/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)上，则不能使用 Azure 信息保护管理员角色。
    
    这些管理员角色仅在配置 Azure 信息保护连接器时才是必需的，在 Azure Sentinel 连接到 Azure 信息保护时不需要这些角色。

- 用于读取和写入 Azure Sentinel 和 Azure 信息保护所使用的 Log Analytics 工作区的权限。

- Azure 信息保护已添加到 Azure 门户。 如果需要此步骤的帮助，请参阅[将 Azure 信息保护添加到 Azure 门户](https://docs.microsoft.com/azure/information-protection/quickstart-viewpolicy#add-azure-information-protection-to-the-azure-portal)。

## <a name="connect-to-azure-information-protection"></a>连接到 Azure 信息保护

如果尚未为 Azure 信息保护配置 Log Analytics 工作区，或者需要更改存储 Azure 信息保护日志记录信息的工作区，请使用以下说明。

1. 在 Azure Sentinel 中，选择 "**数据连接器**"，然后选择 " **Azure 信息保护（预览版）** "。

2. 选择 "**打开连接器" 页面**。

3. 在 "**配置分析（预览版）** " 边栏选项卡上，选择当前用于 Azure Sentinel 的工作区。 如果选择其他工作区，azure 信息保护中的报表数据将不可用于 Azure Sentinel。

4. 选择工作区后，选择 **"确定"** ，此时连接器**状态**应更改为 "**已连接**"。

5. Azure 信息保护中的报表数据存储在所选工作区内的 " **InformationProtectionLogs_CL** " 表中。 
    
    若要为此报表数据使用 Azure Monitor 中的相关架构，请搜索**InformationProtectionEvents**。 有关这些事件函数的信息，请参阅 Azure 信息保护文档中的[事件函数的友好架构参考](https://docs.microsoft.com/azure/information-protection/reports-aip#friendly-schema-reference-for-event-functions)部分。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Azure 信息保护连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
