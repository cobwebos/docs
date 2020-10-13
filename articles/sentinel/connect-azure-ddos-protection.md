---
title: 将 Azure DDoS 保护数据连接到 Azure Sentinel
description: 了解如何将 Azure DDoS 保护数据引入 Azure Sentinel，以便可以查看、分析和调查。
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 09/01/2020
ms.author: yelevin
ms.openlocfilehash: e8e44f69965af1987bd5f023644d966b3caf1c77
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89505804"
---
# <a name="connect-data-from-azure-ddos-protection"></a>连接 Azure DDoS 保护的数据

> [!IMPORTANT]
> Azure Sentinel 中的 Azure DDoS 保护数据连接器目前为公共预览版。
> 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

分布式拒绝服务 (DDoS) 攻击尝试耗尽应用程序的资源，使应用程序对于合法用户不可用。 DDoS 攻击可能会将任何可通过 Internet 公开访问的终结点作为目标。 [Azure DDoS 保护](../virtual-network/ddos-protection-overview.md)与应用程序设计最佳做法相结合，为防范 DDoS 攻击提供了强大的防御。 你可以将 Azure DDoS 保护日志连接到 Azure Sentinel，使你能够查看工作簿中的日志数据，使用它创建自定义警报，并将其合并以改善调查。 

## <a name="prerequisites"></a>必备条件

- 你必须具有 Azure Sentinel 工作区的读取和写入权限。

- 你必须具有已配置的 [Azure DDoS 标准保护计划](../virtual-network/manage-ddos-protection.md#create-a-ddos-protection-plan)。

- 你必须具有已 [启用 Azure DDoS 标准](../virtual-network/manage-ddos-protection.md#enable-ddos-for-a-new-virtual-network)的已配置虚拟网络。

## <a name="connect-to-azure-ddos-protection"></a>连接到 Azure DDoS 保护
    
1. 在 Azure Sentinel 导航菜单中，选择 " **数据连接器**"。

1. 从数据连接器库中选择 " **Azure DDoS 保护** "，并在预览窗格中选择 " **打开连接器** "。

1. 在要连接其日志的所有防火墙上启用 **诊断日志** ：

    1. 选择 " **打开诊断设置" >** "链接"，然后从列表中选择 **公共 IP 地址** 资源。

    1. 选择“+ 添加诊断设置”。 

    1. 在 " **诊断设置** " 屏幕中：
       - 在 "  **诊断设置名称** " 字段中输入名称。

       - 选中 " **发送到 Log Analytics** " 复选框。 其中将显示两个新字段。 选择相关 **订阅** ，并 **Log Analytics 工作区** (Azure Sentinel 所在的位置) 。

       - 标记要引入其日志的规则类型的复选框。 建议 **DDoSProtectionNotifications**、 **DDoSMitigationFlowLogs**和 **DDoSMitigationReports**。

    1. 单击屏幕顶部的“保存”。  对于已启用 DDoS 保护)  (公共 IP 地址的任何其他防火墙，请重复此过程。

1. 若要在 Azure DDoS 防护警报 Log Analytics 中使用相关架构，请搜索 **AzureDiagnostics**。

> [!NOTE]
>
> 使用这个特定的数据连接器，在过去两周内引入数据时，数据连接器库中的连接状态 (指示器和数据类型) 名称旁的连接图标将显示为 " *已连接* (绿色) 。 两周后，如果没有数据引入，连接器将显示为 "已断开连接"。 数据经历的时间越多， *连接* 状态就返回。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Azure DDoS 保护日志连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
