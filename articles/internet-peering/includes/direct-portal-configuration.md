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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775272"
---
1. 在"**创建对等"** 页上，**在"配置"** 选项卡下填写字段，如下所示。

    * 对于**对等式**，选择 *"直接*"。
    * 对于**微软网络**，请选择*AS8075。* 不要使用 ASN 8069 创建对等互连。 它保留给特殊应用程序，并且只由[微软对等互连](mailto:peering@microsoft.com)使用。
    * 选择**SKU**作为*基本免费*。 不要选择*免费高级*，因为它是为特殊应用程序保留的。
    * 选择要设置对等互连的 **"地铁**"位置。

        > [!NOTE]
        > 如果您在所选**的 Metro**位置中已与 Microsoft 建立了对等互连连接，并且首次使用门户在该位置设置对等互连，则现有的对等互连连接将列在 **"对等互连连接**"部分，如下所示。 Microsoft 将自动将这些对等连接转换为 Azure 资源，以便您可以在一个位置管理这些连接以及新连接。 有关详细信息[，请参阅使用门户将旧版直接对等互连转换为 Azure 资源](../howto-legacy-direct-portal.md)。
        >

1. 在 **"对等互连连接**"下，单击 **"创建新**"以为要设置的每个新连接添加一行。

    * 要配置/修改连接设置，请单击行的编辑按钮。

        > [!div class="mx-imgBorder"]
        > ![对等配置 - 直接编辑](../media/setup-direct-conf-tab-edit.png)
    
    * 要删除一行，请单击 **...** 按钮>**删除**。

        > [!div class="mx-imgBorder"]
        > ![对等配置 - 直接编辑](../media/setup-direct-conf-tab-delete.png)

    * 您需要提供连接的所有设置，如下所示。

         > [!div class="mx-imgBorder"]
         > ![对等配置 - 直接连接](../media/setup-direct-conf-tab-connection.png)

        1. 选择需要设置连接的**对等设施**。
        1. **会话地址提供程序**用于确定谁提供了在网络和 Microsoft 之间设置 BGP 会话所需的子网。 如果能够提供子网，请选择*对等项*。 否则选择**微软**和[微软对等将](mailto:peering@microsoft.com)与您联系。 请注意，选择这样做，对于 Microsoft 来说，处理对等请求需要更长的时间。 在某些情况下，Microsoft 可能无法提供子网，这将导致请求拒绝。
        1. 如果选择**会话地址提供程序**作为*对等地址*，则分别在字段 **"会话 IPv4 前缀**"和"**会话 IPv6 前缀**"字段中输入 IPv4 和 IPv6 地址以及前缀掩码。
        1. 输入您将在字段中通告的 IPv4 和 IPv6 前缀的数量 **"最大播发的 IPv4 地址**"和 **"最大通告的 IPv6 地址**"。
        1. 调整**总带宽**滑块以反映连接的带宽。
        1. 单击"**确定"** 以保存连接设置。

1. 重复上述步骤，在之前选择的**Metro**中，在 Microsoft 与您的网络同地连接的任何设施中添加更多连接。

1. 添加所有必需的连接后，单击"**审阅 + 创建**"。

    > [!div class="mx-imgBorder"]
    > ![对等配置选项卡最终](../media/setup-direct-conf-tab-final.png)

1. 请注意，门户运行您输入的信息的基本验证。 这显示在顶部的功能区中，如*正在运行最终验证...*

    > [!div class="mx-imgBorder"]
    > ![对等验证选项卡](../media/setup-direct-review-tab-validation.png)

1. 在它变为*验证通过*后，验证您的信息，并通过单击"**创建**"提交请求。 如果需要修改请求，请单击 **"上一步"** 并重复上述步骤。

    > [!div class="mx-imgBorder"]
    > ![对等提交](../media/setup-direct-review-tab-submit.png)

1. 提交请求后，请等待它完成部署。 如果部署失败，请与[微软对等互连](mailto:peering@microsoft.com)。 成功部署将显示如下。

    > [!div class="mx-imgBorder"]
    > ![窥视成功](../media/setup-direct-success.png)
