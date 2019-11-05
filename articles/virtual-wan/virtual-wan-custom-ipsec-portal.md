---
title: 为 Azure 虚拟 WAN 配置自定义 IPsec 策略：门户 |Microsoft Docs
description: 了解如何使用门户为 Azure 虚拟 WAN 配置自定义 IPsec 策略。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: f37d7f3bfac37253339aab3493fb2c444900e099
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515741"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>使用门户为虚拟 WAN 配置自定义 IPsec 策略

可以在 Azure 门户中为虚拟 WAN 配置自定义 IPsec 策略。 当你希望两个端（本地和 Azure VPN 网关）对 IKE 阶段1和 IKE 阶段2使用相同的设置时，自定义策略非常有用。

## <a name="working-with-custom-policies"></a>使用自定义策略

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>配置策略

1. **找到虚拟中心**。 从浏览器导航到 [Azure 门户](https://aka.ms/azurevirtualwanpreviewfeatures)并使用 Azure 帐户登录。 找到站点的虚拟中心。
2. **选择 VPN 站点**。 在 "中心" 页上，选择要为其设置自定义策略的 VPN 站点。

   ![选择](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **编辑 VPN 连接**。 从**上下文菜单** **中，选择 "** **编辑 VPN 连接**"。

   ![编辑](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **配置设置**。 在 "**编辑 VPN 连接**" 页上，配置设置。 选择“保存”以保存设置。

   ![配置并保存](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>后续步骤

若要详细了解虚拟 WAN，请参阅[虚拟 WAN 概述](virtual-wan-about.md)页。