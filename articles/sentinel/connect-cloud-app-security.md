---
title: 将 Cloud App Security 数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何使用 Microsoft Cloud App Security (MCAS) 连接器来流式传输警报并将 MCAS 中的日志 Cloud Discovery 到 Azure Sentinel。 
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
ms.date: 03/24/2020
ms.author: yelevin
ms.openlocfilehash: 6be2805cab1bcaadb7878be69399e14ffe59d7b0
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89659738"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>连接 Microsoft Cloud App Security 的数据 

利用 [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS) 连接器，你可以将警报和 [CLOUD DISCOVERY 日志](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it) 从 MCAS 流式传输到 Azure Sentinel。 这使你能够深入了解你的云应用，获取复杂分析来识别和对付以来自2200，并控制数据的传播方式。

## <a name="prerequisites"></a>先决条件

- 你的用户必须具有工作区的读取和写入权限。
- 用户必须对工作区的租户具有全局管理员或安全管理员权限。
- 若要将 Cloud Discovery 日志流式传输到 Azure Sentinel，请 [在 Microsoft Cloud App Security 中启用 Azure sentinel 作为你的 SIEM](https://aka.ms/AzureSentinelMCAS)。

> [!IMPORTANT]
> 引入 Cloud Discovery 日志当前为公共预览版。
> 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
 
## <a name="connect-to-cloud-app-security"></a>连接到 Cloud App Security

如果已有 Cloud App Security，请确保已 [在网络上启用](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)它。
如果 Cloud App Security 已部署并引入数据，则可以轻松地将警报数据流式传输到 Azure Sentinel。


1. 在 Azure Sentinel 导航菜单中，选择 " **数据连接器**"。 在连接器列表中，单击 " **Microsoft Cloud App Security** " 磁贴，然后单击右下角的 " **打开连接器页** " 按钮。

1. 选择要流式传输到 Azure Sentinel 的日志;您可以选择 **警报** 并 **Cloud Discovery 日志** (预览版) 。 

1. 单击“应用更改”****。

1. 你可以选择是否希望 Azure Defender (以前的 Azure 安全中心) 在 Azure Sentinel 中自动生成事件。 在 " **创建事件**" 下，选择 " **启用** " 以启用自动根据警报创建事件的默认分析规则。 然后，你可以在 " **分析**" 下的 "  **活动规则** " 选项卡中编辑此规则。

1. 若要使用 Log Analytics 中的相关架构 Cloud App Security 警报，请 `SecurityAlert` 在 "查询" 窗口中键入。 对于 Cloud Discovery 日志架构，请键入 `McasShadowItReporting` 。

> [!NOTE]
> Cloud Discovery 通过将数据基础用户连接聚合到云应用来帮助检测和确定趋势。
>
> 由于 Cloud Discovery 的数据是按天聚合的，因此请注意，最多24小时的最新数据将不会在 Azure Sentinel 中反映出来。 如果低级调查要求更直接的数据，则应直接在原始数据所在的源设备或服务中执行此操作。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Microsoft Cloud App Security 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 使用 [内置](tutorial-detect-threats.md) 或 [自定义](tutorial-detect-threats-custom.md) 规则开始使用 Azure Sentinel 检测威胁。
