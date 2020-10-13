---
title: 为 Azure 虚拟 WAN 配置自定义 IPsec 策略：门户 |Microsoft Docs
description: 了解如何使用门户为 Azure 虚拟 WAN 配置自定义 IPsec 策略。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: 07eae453441ea8bff81d7cdb60f9c46c08a22829
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91851161"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>使用门户为虚拟 WAN 配置自定义 IPsec 策略

可以在 Azure 门户中为虚拟 WAN VPN 连接配置自定义 IPsec 策略。 如果希望两端（本地和 Azure VPN 网关）对“IKE 阶段 1”和“IKE 阶段 2”使用相同的设置，则可使用自定义策略。

## <a name="working-with-custom-policies"></a>使用自定义策略

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>配置策略

1. **找到虚拟中心**。 从浏览器导航到 [Azure 门户](https://aka.ms/azurevirtualwanpreviewfeatures)并使用 Azure 帐户登录。 导航到虚拟 WAN 资源并找到 VPN 站点连接到的虚拟中心。
2. **选择 VPN 站点**。 从 "集线器概述" 页中，单击 " **VPN (站点到站点) ** "，然后选择要为其设置自定义 IPsec 策略的 vpn 站点。

   ![select](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **编辑 VPN 连接**。 从上下文菜单“...”中，选择“编辑 VPN 连接”    。

   ![编辑](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **配置设置**。 在 " **编辑 VPN 连接** " 页上，将 ipsec 设置从默认值更改为 "自定义"，并自定义 ipsec 策略。 选择“保存”以保存设置  。

   ![配置并保存](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>后续步骤

若要详细了解虚拟 WAN，请参阅[虚拟 WAN 概述](virtual-wan-about.md)页。