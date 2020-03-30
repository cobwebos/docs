---
title: Exchange 对等互连演练
titleSuffix: Azure
description: Exchange 对等互连演练
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: bb761afa6d8953b441b6c9541c43b73031719494
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775168"
---
# <a name="exchange-peering-walkthrough"></a>Exchange 对等互连演练

本节介绍设置和管理 Exchange 对等互连所需的步骤。

## <a name="create-an-exchange-peering"></a>创建 Exchange 对等互连
> [!div class="mx-imgBorder"]
> ![交换对等工作流和连接状态](./media/exchange-peering.png)

为了预配 Exchange 对等互连，必须遵循以下步骤：
1. 查看 Microsoft[对等互连策略](https://peering.azurewebsites.net/peering)以了解 Exchange 对等互连的要求。
1. 在[对等互连数据库中](https://www.peeringdb.com/net/694)查找 Microsoft 对等位置和对等工具 ID
1. 使用创建中的说明请求 Exchange 对等位置对等，[并使用 PowerShell 文章修改 Exchange 对等互连](howto-exchange-powershell.md)，了解更多详细信息。
1. 提交对等请求后，Microsoft 将审核该请求，并在需要时与您联系。
1. 批准后，连接状态将更改为"已批准"
1. 在您结束时配置 BGP 会话并通知 Microsoft
1. 我们将使用 DENY 所有策略预配 BGP 会话，并验证端到端。
1. 如果成功，您将收到一条通知，指出对等互连连接状态为"活动"。
1. 然后，将允许流量通过新的对等互连。

请注意，连接状态不应与标准[BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)会话状态混淆。

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>将旧版 Exchange 对等互连转换为 Azure 资源
要将旧版 Exchange 对等互连转换为 Azure 资源，必须遵循以下步骤：
1. 按照[将旧版 Exchange 对等互连转换为 Azure 资源](howto-legacy-exchange-powershell.md)的说明进行操作
1. 提交转化请求后，Microsoft 将审核该请求，并在需要时与您联系。
1. 获得批准后，您将看到连接状态的 Exchange 对等互连为"活动"。

## <a name="deprovision-exchange-peering"></a>取消预配交换对等互连
联系[Microsoft 对等互连](mailto:peering@microsoft.com)以取消预配 Exchange 对等互连。

当为取消预配设置 Exchange 对等互连时，您将看到连接状态为 **"挂起删除"**

> [!NOTE]
> 如果运行 PowerShell cmdlet 以在连接状态为"预配启动"或"预配完成"时删除 Exchange 对等对等互连，则操作将失败。

## <a name="next-steps"></a>后续步骤

* 了解[与 Microsoft 建立对等互连的先决条件](prerequisites.md)。
