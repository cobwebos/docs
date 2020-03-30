---
title: 为 Azure 虚拟 WAN 配置自定义 IPsec 策略：门户 |微软文档
description: 了解如何使用门户为 Azure 虚拟 WAN 配置自定义 IPsec 策略。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: f37d7f3bfac37253339aab3493fb2c444900e099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515741"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>使用门户为虚拟 WAN 配置自定义 IPsec 策略

您可以在 Azure 门户中为虚拟 WAN 配置自定义 IPsec 策略。 当您希望双方（本地和 Azure VPN 网关）对 IKE 阶段 1 和 IKE 阶段 2 使用相同的设置时，自定义策略非常有用。

## <a name="working-with-custom-policies"></a>使用自定义策略

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>配置策略

1. **找到虚拟中心**。 从浏览器导航到 [Azure 门户](https://aka.ms/azurevirtualwanpreviewfeatures)并使用 Azure 帐户登录。 查找站点的虚拟中心。
2. **选择 VPN 站点**。 从中心页面中，选择要为其设置自定义策略的 VPN 站点。

   ![select](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **编辑 VPN 连接**。 从**上下文菜单****..."，** 选择**编辑VPN连接**。

   ![编辑](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **配置设置**。 在 **"编辑 VPN 连接**"页上，配置设置。 选择 **"保存"** 以保存设置。

   ![配置和保存](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>后续步骤

若要详细了解虚拟 WAN，请参阅[虚拟 WAN 概述](virtual-wan-about.md)页。