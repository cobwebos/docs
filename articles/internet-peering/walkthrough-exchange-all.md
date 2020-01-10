---
title: Exchange 对等
titleSuffix: Azure
description: Exchange 对等
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: bb761afa6d8953b441b6c9541c43b73031719494
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775168"
---
# <a name="exchange-peering-walkthrough"></a>Exchange 对等

本部分介绍设置和管理 Exchange 对等互连所需执行的步骤。

## <a name="create-an-exchange-peering"></a>创建 Exchange 对等互连
> [!div class="mx-imgBorder"]
> ![Exchange 对等互连工作流和连接状态](./media/exchange-peering.png)

为了预配 Exchange 对等互连，必须遵循以下步骤：
1. 查看 Microsoft 对[等互连策略](https://peering.azurewebsites.net/peering)，了解 Exchange 对等互连的要求。
1. 在[PeeringDB](https://www.peeringdb.com/net/694)中查找 Microsoft 对等互连位置和对等互连设施 id
1. 有关更多详细信息，请按照使用[PowerShell 创建和修改 exchange 对等互连](howto-exchange-powershell.md)中的说明请求对等互连位置的交换对等互连。
1. 提交对等互连请求后，Microsoft 将查看请求，并根据需要与你联系。
1. 批准后，连接状态将更改为 "已批准"
1. 在最终配置 BGP 会话并通知 Microsoft
1. 我们将预配 BGP 会话，其中包含拒绝所有策略并验证端对端。
1. 如果成功，您将收到一条通知，指出对等互连连接状态处于活动状态。
1. 然后，将通过新对等互连允许流量。

请注意，不会将连接状态与标准[BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)会话状态混淆。

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>将旧式 Exchange 对等互连转换为 Azure 资源
若要将旧的 Exchange 对等互连转换为 Azure 资源，必须遵循以下步骤：
1. 按照[将旧版 Exchange 对等互连转换为 Azure 资源](howto-legacy-exchange-powershell.md)中的说明进行操作
1. 提交转换请求后，Microsoft 将查看请求，并根据需要与你联系。
1. 批准后，你将看到 Exchange 对等互连，其中的连接状态为 "活动"。

## <a name="deprovision-exchange-peering"></a>取消预配 Exchange 对等互连
与[Microsoft 对等互连](mailto:peering@microsoft.com)联系以取消预配 Exchange 对等互连。

当 Exchange 对等互连设置为取消预配时，你会看到连接状态为 " **PendingRemove** "。

> [!NOTE]
> 如果在连接状态为 "ProvisioningStarted" 或 "ProvisioningCompleted" 时运行 PowerShell cmdlet 来删除 Exchange 对等互连，则操作将失败。

## <a name="next-steps"></a>后续步骤

* 了解[设置与 Microsoft 的对等互连的先决条件](prerequisites.md)。
