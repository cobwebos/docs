---
title: 将 Microsoft 365 Defender 原始数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何从 Microsoft 365 Defender 引入 Azure Sentinel 的原始事件数据。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: e9e892ae08c65ee5c5008bbdc1b419d8dec30183
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933589"
---
# <a name="connect-data-from-microsoft-365-defender-to-azure-sentinel"></a>将数据从 Microsoft 365 Defender 连接到 Azure Sentinel

> [!IMPORTANT]
>
> **Microsoft 365 Defender** 以前称为 **Microsoft 威胁防护** 或 **MTP**。
>
> **Microsoft defender For Endpoint** 以前称为 " **Microsoft Defender 高级威胁防护** " 或 **MDATP**。
>
> 你可能会看到旧名称在一段时间内仍在使用。

## <a name="background"></a>背景

使用新的 [Microsoft 365 Defender](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-threat-protection) 连接器，你可以将 **高级搜寻** 日志（一种类型的原始事件 Microsoft 365 数据）流式传输到 Azure Sentinel。 

通过将 [Microsoft defender For endpoint (MDATP) ](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) 集成到 Microsoft 365 defender 安全机制，你现在可以使用 Microsoft 365 Defender 连接器收集 microsoft Defender for endpoint [高级搜寻](https://aka.ms/mdatpAH) 事件，并将它们直接流式传输到 Azure Sentinel 工作区中的新用途构建的表中。 这些表是在 Microsoft 365 Defender 门户中使用的同一架构上构建的，可让你完全访问一组完整的高级搜寻日志，并允许你执行以下操作：

- 轻松将现有 Microsoft Defender ATP 高级搜寻查询复制到 Azure Sentinel。

- 使用原始事件日志，为警报、搜寻和调查提供其他见解，并将事件与 Azure Sentinel 中其他数据源的数据关联起来。

- 存储保留的保留期，超过 Microsoft Defender for Endpoint 或 Microsoft 365 Defender 的30天的保留期。 为此，可以配置工作区的保留期，或在 Log Analytics 中配置每个表的保留期。

> [!IMPORTANT]
>
> Microsoft 365 Defender 连接器目前为公共预览版。 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必备知识

- 你必须具有 Microsoft Defender for Endpoint 的有效许可证，如为 [终结点部署设置 Microsoft defender](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing)中所述。 

- 你的用户必须在 Azure Active Directory) 的租户 (上分配全局管理员角色。

## <a name="connect-to-microsoft-365-defender"></a>连接到 Microsoft 365 Defender

如果已部署 Microsoft Defender for Endpoint 并引入你的数据，则可以轻松地将事件日志流式传输到 Azure Sentinel。

1. 在 Azure Sentinel 中，选择 " **数据连接器**"，从库中选择 " **Microsoft 365 Defender (预览") ** ，然后选择 " **打开连接器" 页面**。

1. 可从其对应的高级搜寻表中收集以下类型的事件。 标记要收集的事件类型的复选框：

    | 事件类型 | 表名称 |
    |-|-|
    | 计算机信息 (包括 OS 信息)  | DeviceInfo |
    | 计算机的网络属性 | DeviceNetworkInfo |
    | 进程创建和相关事件 | DeviceProcessEvents |
    | 网络连接和相关事件 | DeviceNetworkEvents |
    | 文件创建、修改和其他文件系统事件 | DeviceFileEvents |
    | 创建和修改注册表项 | DeviceRegistryEvents |
    | 登录和其他身份验证事件 | DeviceLogonEvents |
    | DLL 加载事件 | DeviceImageLoadEvents |
    | 其他事件类型 | DeviceEvents |
    |

1. 单击“应用更改”****。 

1. 若要在 Log Analytics 中查询高级搜寻表，请在 "查询" 窗口中输入上述列表中的表名。

## <a name="verify-data-ingestion"></a>验证数据引入

"连接器" 页中的数据图指示您正在引入数据。 你会注意到，它显示了在所有已启用的表中聚合事件卷的单个行。 启用连接器后，可以使用以下 KQL 查询为单个表生成类似的事件卷图形 (将 *DeviceEvents* 表更改为所选) 的所需表：

```kusto
let Now = now();
(range TimeGenerated from ago(14d) to Now-1d step 1d
| extend Count = 0
| union isfuzzy=true (
    DeviceEvents
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now)
)
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now)
| sort by TimeGenerated
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events")
| render timechart
```

在 " **后续步骤** " 选项卡中，你将找到几个已包括的示例查询。 可以在该位置上运行它们，也可以修改并保存它们。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何使用 Microsoft 365 Defender 连接器，将 Microsoft Defender for Endpoint 中的原始事件数据获取到 Azure Sentinel 中。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。
