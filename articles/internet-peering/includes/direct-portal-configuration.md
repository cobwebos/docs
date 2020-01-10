---
title: include 文件
titleSuffix: Azure
description: include 文件
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 2a42ba8809e6895c9eea9f8762513b7fcaa9eb3a
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775272"
---
1. 在 "**创建对等互连**" 页的 "**配置**" 选项卡下，按如下所示填写字段。

    * 对于 "对**等类型**"，请选择 "*直接*"。
    * 对于**Microsoft 网络**，请选择 " *AS8075*"。 不要与 ASN 8069 创建对等互连。 它是为特殊应用程序保留的，仅供[Microsoft 对等互连](mailto:peering@microsoft.com)使用。
    * 选择 " **SKU**为*基本免费*"。 请勿选择 "*高级免费*"，因为它是为特殊应用程序保留的。
    * 选择要设置对等互连的**地铁**位置。

        > [!NOTE]
        > 如果在所选**地铁**位置与 Microsoft 建立了对等互连，并且首次在该位置设置对等互连，则会在 "对**等互连连接**" 部分中列出现有的对等互连连接，如下所示。 Microsoft 将自动将这些对等互连连接转换为 Azure 资源，以便可以将它们与新连接一起在同一位置进行管理。 有关详细信息，请参阅[使用门户将旧直接对等互连转换为 Azure 资源](../howto-legacy-direct-portal.md)。
        >

1. 在 "对**等连接**" 下，单击 "**新建**" 以便为要设置的每个新连接添加一行。

    * 若要配置/修改连接设置，请单击行的 "编辑" 按钮。

        > [!div class="mx-imgBorder"]
        > ![对等互连配置-直接编辑](../media/setup-direct-conf-tab-edit.png)
    
    * 若要删除行，请单击 **"..." 按钮**> **delete**。

        > [!div class="mx-imgBorder"]
        > ![对等互连配置-直接编辑](../media/setup-direct-conf-tab-delete.png)

    * 需要为连接提供所有设置，如下所示。

         > [!div class="mx-imgBorder"]
         > ![对等互连配置-直接连接](../media/setup-direct-conf-tab-connection.png)

        1. 选择需要设置连接的对**等互连设施**。
        1. **会话地址提供程序**用于确定哪些用户提供了在您的网络和 Microsoft 之间设置 BGP 会话所需的子网。 如果能够提供子网，则选择 "*对等*"。 否则，请选择 " **microsoft**和[microsoft 对等互连](mailto:peering@microsoft.com)" 将与你联系。 请注意，选择此操作将花费较长的时间来处理对等请求。 在某些情况下，Microsoft 可能无法提供将导致请求拒绝的子网。
        1. 如果选择了 "**会话地址提供程序**" 作为*对等*互连，请分别**在字段中**输入 ipv4 和 IPv6 地址以及前缀掩码和**会话 IPv6 前缀**。
        1. 输入每个字段中要播发的 IPv4 和 IPv6 前缀的数量，它们分别是**播发的 ipv4 地址**和**最大播发 IPv6 地址**。
        1. 调整 "**总带宽**" 滑块，以反映连接的带宽。
        1. 单击 **"确定"** 保存连接设置。

1. 重复上述步骤，在 Microsoft 与你的网络的任何场所中添加更多连接，这种情况在之前选择的**地铁**范围内。

1. 添加所有所需的连接后，单击 "**审核 + 创建**"。

    > [!div class="mx-imgBorder"]
    > ![对等互连配置选项卡最终](../media/setup-direct-conf-tab-final.png)

1. 观察门户对输入的信息运行基本验证。 这会显示在顶部的功能区中，如*运行最终验证 ...* 。

    > [!div class="mx-imgBorder"]
    > ![对等验证 "选项卡](../media/setup-direct-review-tab-validation.png)

1. 完成*验证*后，请通过单击 "**创建**" 来验证你的信息并提交请求。 如果需要修改请求，请单击 "上**一步**"，然后重复上述步骤。

    > [!div class="mx-imgBorder"]
    > ![对等互连](../media/setup-direct-review-tab-submit.png)

1. 提交请求后，请等待它完成部署。 如果部署失败，请联系[Microsoft 对等互连](mailto:peering@microsoft.com)。 成功的部署将如下所示。

    > [!div class="mx-imgBorder"]
    > ![对等互连成功](../media/setup-direct-success.png)
