---
title: include 文件
description: include 文件
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b079ede0845de3794507691bc3c252930e2a6604
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977150"
---
1. 打开 [Azure 门户](https://portal.azure.com)。 导航到要连接的虚拟机，然后选择“连接”。 从下拉列表中选择“Bastion”。

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm.png" alt-text="选择“Bastion”" border="false":::

1. 从下拉列表中选择“Bastion”后，将显示一条侧边栏，其中包含三个选项卡：RDP、SSH 和 Bastion。 由于已针对虚拟网络预配了 Bastion，因此默认情况下，“Bastion”选项卡处于活动状态。 选择“使用 Bastion”。

   :::image type="content" source="./media/bastion-vm-rdp/use-bastion.png" alt-text="选择“Bastion”" border="false":::

1. 在“使用 Azure Bastion 连接”页上，输入虚拟机的用户名和密码，然后选择“连接” 。

   :::image type="content" source="./media/bastion-vm-rdp/host.png" alt-text="选择“Bastion”" border="false":::

1. 通过 Bastion 连接到此虚拟机的 RDP 将使用端口 443 和 Bastion 服务在 Azure 门户中（通过 HTML5）直接打开。

   :::image type="content" source="./media/bastion-vm-rdp/connection.png" alt-text="选择“Bastion”" border="false":::
