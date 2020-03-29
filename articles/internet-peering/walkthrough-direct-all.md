---
title: 直接对等互连演练
titleSuffix: Azure
description: 直接对等互连演练
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d88fcfc4d3e073bf544f2ca0f4d01dbe305b45da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775506"
---
# <a name="direct-peering-walkthrough"></a>直接对等互连演练

本节介绍设置和管理直接对等互连所需的步骤。

## <a name="create-a-direct-peering"></a>创建直接对等互连
> [!div class="mx-imgBorder"]
> ![直接对等工作流和连接状态](./media/direct-peering.png)

必须遵循以下步骤才能预配直接对等互连：
1. 查看 Microsoft[对等互连策略](https://peering.azurewebsites.net/peering)以了解直接对等互连的要求。
1. 按照["创建"或修改 Direct 对等互连](howto-direct-powershell.md)中的说明来提交对等请求。
1. 提交对等请求后，Microsoft 将使用您注册的电子邮件地址联系，以提供 LOA（授权书）或其他信息。
1. 审核对等请求后，连接状态将更改为预配启动。
1. 你需要：
    1. 根据 LOA 完成布线
    1. （可选）使用 169.254.0/16 执行链路测试
    1. 配置 BGP 会话，然后通知我们。
1. Microsoft 提供 BGP 会话与 DENY 所有策略并验证端到端。
1. 如果成功，您将收到一条通知，指出对等互连连接状态为"活动"。
1. 然后，将允许流量通过新的对等互连。

请注意，连接状态不应与标准[BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)会话状态混淆。

## <a name="convert-a-legacy-direct-peering-to-azure-resource"></a>将旧版直接对等互连转换为 Azure 资源
要将旧版直接对等互连转换为 Azure 资源，必须遵循以下步骤：
1. 按照[将旧版直接对等互连](howto-legacy-direct-powershell.md)转换为 Azure 资源的说明进行操作
1. 提交转化请求后，Microsoft 将审核该请求，并在需要时与您联系。
1. 获得批准后，您将看到连接状态为"直接对等互连"为"活动"。

## <a name="deprovision-direct-peering"></a>取消预配 直接对等互连
请与[Microsoft 对等互连](mailto:peering@microsoft.com)团队联系以取消预配直接对等互连。

当为取消预配设置直接对等互连时，您将看到连接状态为 **"挂起删除"**

> [!NOTE]
> 如果运行 PowerShell cmdlet 以在连接状态预配启动或预配完成时删除直接对等互连，则操作将失败。

## <a name="next-steps"></a>后续步骤

* 了解[与 Microsoft 建立对等互连的先决条件](prerequisites.md)。
