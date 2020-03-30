---
title: 将 Azure 信息保护连接到 Azure 哨兵
description: 了解如何在 Azure Sentinel 中连接 Azure 信息保护数据。
services: sentinel
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 396fd7c4289c9d02d451b26f5fb6299acd31e2a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588546"
---
# <a name="connect-data-from-azure-information-protection"></a>连接 Azure 信息保护的数据

> [!IMPORTANT]
> Azure 哨兵中的 Azure 信息保护数据连接器当前处于公共预览版中。
> 此功能在没有服务级别协议的情况下提供，不建议用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

通过配置[Azure 信息保护](https://azure.microsoft.com/services/information-protection/)数据连接器，可以将 Azure 信息保护中的日志记录信息流式传输到 Azure Sentinel 中。 Azure 信息保护可帮助您控制和保护敏感数据，无论这些数据存储在云中还是本地。

如果已配置[Azure 信息保护的中央报告](https://docs.microsoft.com/azure/information-protection/reports-aip)，以便此服务的日志记录信息存储在当前为 Azure Sentinel 选择的日志分析工作区中，则可以跳过此数据连接器的配置。 Azure 信息保护的日志记录信息已可供 Azure 哨兵使用。

但是，如果从 Azure 信息保护中记录信息将到与当前为 Azure Sentinel 选择的日志分析工作区不同的工作区，请执行以下操作之一：

- 更改 Azure 哨兵中选择的工作区。

- 更改 Azure 信息保护工作区，可以通过配置此数据连接器来执行此操作。
    
    如果更改工作区，Azure 信息保护的新报告数据现在将存储在用于 Azure Sentinel 的工作区中，并且 Azure Sentinel 不可用历史数据。 此外，如果为自定义查询、警报或 REST API 配置了以前的工作区，则必须为 Azure Sentinel 工作区重新配置这些工作区，如果要继续使用它们进行 Azure 信息保护。 对于使用 Azure 信息保护的客户端和服务，无需重新配置。

## <a name="prerequisites"></a>先决条件

- 租户的以下 Azure AD 管理员角色之一： 
    - Azure 信息保护管理员
    - 安全管理员
    - 法规管理员
    - 合规性数据管理员
    - 全局管理员
    
    > [!NOTE]
    > 如果您的租户位于[统一标签平台上](/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)，则无法使用 Azure 信息保护管理员角色。
    
    这些管理员角色仅需要配置 Azure 信息保护连接器，并且在 Azure Sentinel 连接到 Azure 信息保护时不需要这些管理员角色。

- 读取和写入用于 Azure 哨兵和 Azure 信息保护的日志分析工作区的权限。

- Azure 信息保护已添加到 Azure 门户。 如果需要有关此步骤的帮助，请参阅[向 Azure 门户添加 Azure 信息保护](https://docs.microsoft.com/azure/information-protection/quickstart-viewpolicy#add-azure-information-protection-to-the-azure-portal)。

## <a name="connect-to-azure-information-protection"></a>连接到 Azure 信息保护

如果尚未为 Azure 信息保护配置日志分析工作区，或者需要更改存储 Azure 信息保护日志记录信息的工作区，请使用以下说明。

1. 在 Azure 哨兵中，选择**数据连接器**，然后选择**Azure 信息保护（预览）。**

2. 选择 **"打开连接器"页**。

3. 在 **"配置分析（预览）"** 边栏选项卡上，选择当前用于 Azure Sentinel 的工作区。 如果选择其他工作区，Azure 信息保护的报告数据将不适用于 Azure 哨兵。

4. 选择工作区后，选择 **"确定"，** 并且连接器**状态**现在应更改为 **"已连接**"。

5. Azure 信息保护的报告数据存储在所选工作区中的**InformationProtectionLogs_CL**表中。 
    
    要使用此报告数据的 Azure 监视器中的相关架构，搜索**信息保护事件**。 有关这些事件函数的信息，请参阅 Azure 信息保护文档中[的事件函数友好架构参考](https://docs.microsoft.com/azure/information-protection/reports-aip#friendly-schema-reference-for-event-functions)部分。

## <a name="next-steps"></a>后续步骤

在本文档中，您学习了如何将 Azure 信息保护连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
