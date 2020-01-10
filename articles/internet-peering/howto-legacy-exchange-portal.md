---
title: 使用门户将旧直接对等互连转换为 Azure 资源
titleSuffix: Azure
description: 使用门户将旧直接对等互连转换为 Azure 资源
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a40c7bbc9f37135814b7bba3396d368faf97a166
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775194"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-the-portal"></a>使用门户将旧版 Exchange 对等互连转换为 Azure 资源

本文介绍如何使用门户将现有的旧式 Exchange 对等互连转换为 Azure 资源。

如果愿意，可以使用[PowerShell](howto-legacy-exchange-powershell.md)完成本指南。

## <a name="before-you-begin"></a>开始之前
* 开始配置之前，请查看[先决条件](prerequisites.md)和[Exchange 对等互连演练](walkthrough-exchange-all.md)。

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>将旧式 Exchange 对等互连转换为 Azure 资源

### <a name="sign-in-to-portal-and-select-your-subscription"></a>登录到门户并选择你的订阅
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>转换旧版 Exchange 对等互连

可以使用对**等互连**资源转换旧的对等互连连接。

#### <a name="launch-resource-and-configure-basic-settings"></a>启动资源并配置基本设置
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>配置连接并提交
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name=get></a>验证 Exchange 对等互连
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>其他资源

有关详细信息，请访问[Internet 对等互连常见问题解答](faqs.md)

## <a name="next-steps"></a>后续步骤

* [使用门户创建或修改 Exchange 对等互连](howto-exchange-portal.md)
