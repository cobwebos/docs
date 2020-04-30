---
title: 下载 Azure 虚拟 WAN 全局或基于集线器的 VPN 配置文件 |Microsoft Docs
description: 了解有关虚拟 WAN 自动化可缩放的分支到分支连接、可用区域和合作伙伴的信息。
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 4/20/2020
ms.author: alzam
ms.openlocfilehash: b63bb861f4df087f852bb1bf599d32100d063f7e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733184"
---
# <a name="download-a-global-or-hub-based-profile-for-user-vpn-clients"></a>为用户 VPN 客户端下载全局或基于中心的配置文件

Azure 虚拟 WAN 为远程用户提供两种类型的连接：全局连接和基于中心的连接。 使用以下部分了解和下载配置文件。 

> [!IMPORTANT]
> RADIUS 身份验证仅支持基于集线器的配置文件。

## <a name="global-profile"></a>全局配置文件

配置文件指向包含所有活动用户 VPN 集线器的负载均衡器。 用户定向到最靠近用户的地理位置的中心。 当用户经常出差到不同位置时，这种类型的连接会很有用。 下载**全局**配置文件：

1. 导航到虚拟 WAN。
2. 单击 "**用户 VPN 配置**"。
3. 突出显示要为其下载配置文件的配置。
4. 单击 "**下载虚拟 WAN 用户 VPN 配置文件**"。

   ![全局配置文件](./media/global-hub-profile/global1.png)

## <a name="hub-based-profile"></a>基于集线器的配置文件

配置文件指向单个集线器。 用户只能使用此配置文件连接到特定的中心。 下载**基于集线器**的配置文件：

1. 导航到虚拟 WAN。
2. 单击 "概述" 页中的 "**中心**"。

    ![中心配置文件1](./media/global-hub-profile/hub1.png)
3. 单击 "**用户 VPN （点到站点）**"。
4. 单击 "**下载虚拟中心用户 VPN 配置文件**"。

   ![中心配置文件2](./media/global-hub-profile/hub2.png)
5. 请检查**EAPTLS**。
6. 单击 "**生成并下载配置文件**"。

   ![中心配置文件3](./media/global-hub-profile/download.png)

## <a name="next-steps"></a>后续步骤

若要详细了解虚拟 WAN，请参阅[虚拟 WAN 概述](virtual-wan-about.md)页。
