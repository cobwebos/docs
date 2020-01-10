---
title: 使用门户创建或修改直接对等互连
titleSuffix: Azure
description: 使用门户创建或修改直接对等互连
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 67a4944082d3ebc14fd564eedee0310afe6e3ff5
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775324"
---
# <a name="create-or-modify-a-direct-peering-using-the-portal"></a>使用门户创建或修改直接对等互连

本文介绍如何使用门户创建 Microsoft 直接对等互连。 本文还介绍了如何检查资源的状态、对其进行更新、删除和取消预配。

如果愿意，可以使用[PowerShell](howto-direct-powershell.md)完成本指南。

## <a name="before-you-begin"></a>开始之前
* 开始配置之前，请查看[先决条件](prerequisites.md)和[直接对等互连演练](walkthrough-direct-all.md)。
* 如果你已将与 Microsoft 的直接对等互连（不会转换为 Azure 资源），请参阅[使用门户将旧直接对等互连转换为 azure 资源](howto-legacy-direct-portal.md)

## <a name="create-and-provision-a-direct-peering"></a>创建和预配直接对等互连

### <a name="sign-in-to-portal-and-select-your-subscription"></a>登录到门户并选择你的订阅
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>创建直接对等互连

可以使用对**等互连**资源创建新的对等互连请求。

#### <a name="launch-resource-and-configure-basic-settings"></a>启动资源并配置基本设置
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>配置连接并提交
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name=get></a>验证直接对等互连
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify"></a>修改直接对等互连
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="delete"></a>取消预配对等互连
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>后续步骤

* [使用门户创建或修改 Exchange 对等互连](howto-exchange-portal.md)。
* [使用门户将旧版 Exchange 对等互连转换为 Azure 资源](howto-legacy-exchange-portal.md)。

## <a name="additional-resources"></a>其他资源

有关详细信息，请访问[Internet 对等互连常见问题解答](faqs.md)
