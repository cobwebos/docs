---
title: 直接对等演练
titleSuffix: Azure
description: 直接对等演练
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d88fcfc4d3e073bf544f2ca0f4d01dbe305b45da
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775506"
---
# <a name="direct-peering-walkthrough"></a>直接对等演练

本部分介绍设置和管理直接对等互连所需执行的步骤。

## <a name="create-a-direct-peering"></a>创建直接对等互连
> [!div class="mx-imgBorder"]
> ![直接对等互连工作流和连接状态](./media/direct-peering.png)

为了预配直接对等互连，必须遵循以下步骤：
1. 查看 Microsoft 对[等互连策略](https://peering.azurewebsites.net/peering)以了解直接对等互连的要求。
1. 按照[创建或修改直接对等互连](howto-direct-powershell.md)中的说明提交对等请求。
1. 提交对等互连请求后，Microsoft 将使用你的注册电子邮件地址进行联系，以提供 LOA （授权信）或其他信息。
1. 对等互连请求获得批准后，连接状态将更改为 ProvisioningStarted。
1. 你需要：
    1. 根据 LOA 完成布线
    1. （可选）使用 169.254.0.0/16 执行链接测试
    1. 配置 BGP 会话，然后通知我们。
1. Microsoft 预配具有拒绝所有策略的 BGP 会话并验证端对端。
1. 如果成功，您将收到一条通知，指出对等互连连接状态处于活动状态。
1. 然后，将通过新对等互连允许流量。

请注意，不会将连接状态与标准[BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)会话状态混淆。

## <a name="convert-a-legacy-direct-peering-to-azure-resource"></a>将旧直接对等互连转换为 Azure 资源
若要将旧直接对等互连转换为 Azure 资源，必须遵循以下步骤：
1. 按照[将旧直接对等互连转换为 Azure 资源](howto-legacy-direct-powershell.md)中的说明进行操作
1. 提交转换请求后，Microsoft 将查看请求，并根据需要与你联系。
1. 批准后，你将看到直接对等互连，连接状态为 "活动"。

## <a name="deprovision-direct-peering"></a>取消预配直接对等
请与[Microsoft 对等](mailto:peering@microsoft.com)团队联系以取消预配直接对等互连。

如果将直接对等互连设置为取消设置，则会看到连接状态为 " **PendingRemove** "。

> [!NOTE]
> 如果在 ConnectionState 为 ProvisioningStarted 或 ProvisioningCompleted 时运行 PowerShell cmdlet 删除直接对等互连，则操作将失败。

## <a name="next-steps"></a>后续步骤

* 了解[设置与 Microsoft 的对等互连的先决条件](prerequisites.md)。
