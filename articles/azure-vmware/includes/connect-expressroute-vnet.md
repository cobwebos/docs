---
title: 将 ExpressRoute 连接到虚拟网络网关
description: 将 ExpressRoute 连接到虚拟网络网关的步骤。
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 214ef9c01193b238c8e456ef2809f7a2edbdb6c7
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91598169"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-configure-networking.md -->

1. 导航到在上一教程中创建的私有云，在“管理”下选择“连接”，然后选择“ExpressRoute”选项卡  。

1. 复制授权密钥。 如果没有授权密钥，则需要创建一个，请选择“+ 请求授权密钥”。

   :::image type="content" source="../media/expressroute-global-reach/start-request-auth-key.png" alt-text="复制授权密钥。如果没有授权密钥，则需要创建一个，请选择“+ 请求授权密钥”" border="true" lightbox="../media/expressroute-global-reach/start-request-auth-key.png":::。

1. 导航到在上一步中创建的虚拟网络网关，在“设置”下选择“连接” 。 在“连接”页上，选择“+ 添加” 。

1. 在“添加连接”页上为字段提供值，然后选择“确定”。 

   | 字段 | 值 |
   | --- | --- |
   | **名称**  | 输入连接的名称。  |
   | **连接类型**  | 选择“ExpressRoute”。  |
   | **兑换授权**  | 确保选中此框。  |
   | **虚拟网络网关** | 前面创建的虚拟网络网关。  |
   | **授权密钥**  | 从资源组的“ExpressRoute”选项卡复制并粘贴授权密钥。 |
   | **对等线路 URI**  | 从资源组的“ExpressRoute”选项卡复制并粘贴 ExpressRoute ID。  |

   :::image type="content" source="../media/expressroute-global-reach/open-cloud-shell.png" alt-text="复制授权密钥。如果没有授权密钥，则需要创建一个，请选择“+ 请求授权密钥”" border="true" lightbox="../media/expressroute-global-reach/open-cloud-shell.png":::

这会在 ExpressRoute 线路与虚拟网络之间创建连接。