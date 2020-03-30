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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775324"
---
# <a name="create-or-modify-a-direct-peering-using-the-portal"></a>使用门户创建或修改直接对等互连

本文介绍如何使用门户创建 Microsoft 直接对等互连。 本文还演示如何检查资源的状态、更新资源或删除和取消预配资源。

如果您愿意，您可以使用[PowerShell](howto-direct-powershell.md)完成本指南。

## <a name="before-you-begin"></a>开始之前
* 在开始配置之前，请查看[先决条件](prerequisites.md)和[直接对等演练](walkthrough-direct-all.md)。
* 如果已与 Microsoft 没有直接对等互连（未转换为 Azure 资源），请参阅[使用门户将旧版直接对等互连转换为 Azure 资源](howto-legacy-direct-portal.md)

## <a name="create-and-provision-a-direct-peering"></a>创建和预配直接对等互连

### <a name="sign-in-to-portal-and-select-your-subscription"></a>登录到门户并选择订阅
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>创建直接对等互连

可以使用**对等互连**资源创建新的对等互连请求。

#### <a name="launch-resource-and-configure-basic-settings"></a>启动资源并配置基本设置
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>配置连接并提交
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>验证直接对等互连
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>修改直接对等互连
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>取消预配直接对等互连
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>后续步骤

* [使用门户创建或修改 Exchange 对等互连](howto-exchange-portal.md)。
* [使用门户将旧 Exchange 对等互连转换为 Azure 资源](howto-legacy-exchange-portal.md)。

## <a name="additional-resources"></a>其他资源

有关详细信息，请访问[互联网对等常见问题解答](faqs.md)
