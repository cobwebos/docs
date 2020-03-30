---
title: 下载 Azure 虚拟 WAN 全局或基于集线器的 VPN 配置文件 |微软文档
description: 了解有关虚拟 WAN 自动化可缩放的分支到分支连接、可用区域和合作伙伴的信息。
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 1/31/2020
ms.author: alzam
ms.openlocfilehash: 3b7e765dbd024d46939e8989993f0c882b2a8f4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965224"
---
# <a name="download-a-global-or-hub-based-profile-for-user-vpn-clients"></a>下载用户 VPN 客户端的全局或基于中心的配置文件

Azure 虚拟 WAN 为远程用户提供两种类型的连接：全局和基于集线器。 使用以下部分了解和下载配置文件。

## <a name="global-profile"></a>全球概况

配置文件指向包含所有活动用户 VPN 集线器的负载均衡器。 用户被定向到最接近用户的地理位置的中心。 当用户频繁前往不同位置时，这种类型的连接非常有用。 要下载**全局**配置文件：

1. 导航到虚拟 WAN。
2. 单击**用户 VPN 配置**。
3. 突出显示要为其下载配置文件的配置。
4. 单击 **"下载虚拟 WAN 用户 VPN 配置文件**"。

   ![全球概况](./media/global-hub-profile/global1.png)

## <a name="hub-based-profile"></a>基于集线器的配置文件

配置文件指向单个中心。 用户只能使用此配置文件连接到特定中心。 要下载**基于集线器的**配置文件：

1. 导航到虚拟 WAN。
2. 单击"概述"页中的 **"中心**"。

    ![集线器配置文件 1](./media/global-hub-profile/hub1.png)
3. 单击**用户 VPN（指向站点）**。
4. 单击 **"下载虚拟中心用户 VPN 配置文件**"。

   ![集线器配置文件 2](./media/global-hub-profile/hub2.png)
5. 检查**EAPTLS**。
6. 单击 **"生成并下载配置文件**"。

   ![集线器配置文件 3](./media/global-hub-profile/download.png)

## <a name="next-steps"></a>后续步骤

若要详细了解虚拟 WAN，请参阅[虚拟 WAN 概述](virtual-wan-about.md)页。
