---
title: 使用门户将对等 ASN 关联到 Azure 订阅
titleSuffix: Azure
description: 使用门户将对等 ASN 关联到 Azure 订阅
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d3737be5a3186774f230aef9d932464a27a764f4
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775636"
---
# <a name="associate-peer-asn-to-azure-subscription-using-the-portal"></a>使用门户将对等 ASN 关联到 Azure 订阅

提交对等互连请求之前，首先应使用以下步骤将 ASN 与 Azure 订阅相关联。

如果愿意，可以使用[PowerShell](howto-subscription-association-powershell.md)完成本指南。

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>创建 PeerAsn，将 ASN 与 Azure 订阅关联

### <a name="sign-in-to-the-portal"></a>登录到门户
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-peerasn"></a>创建 PeerAsn
可以创建新的 PeerAsn 资源，将自治系统编号（ASN）与 Azure 订阅相关联。 可以通过为需要关联的每个 ASN 创建**PeerAsn** ，将多个 asn 关联到一个订阅。

1. 单击 "**创建资源**" > "**全部查看**"。

    > [!div class="mx-imgBorder"]
    > ![搜索 PeerAsn](./media/peerasn-seeall.png)

1. 在搜索框中搜索 " *PeerAsn* "，然后在键盘上按*Enter* 。 在结果中，单击 " **PeerAsn**资源"。

    > [!div class="mx-imgBorder"]
    > ![启动 PeerAsn](./media/peerasn-launch.png)

1. 启动**PeerAsn**后，单击 "**创建**"。

    > [!div class="mx-imgBorder"]
    > ![创建 PeerAsn](./media/peerasn-create.png)

1. 在 "**关联一方 ASN** " 页上的 "**基本**信息" 选项卡下，按如下所示填写字段。

    > [!div class="mx-imgBorder"]
    > ![PeerAsn 基本信息选项卡](./media/peerasn-basics-tab.png)

    * **名称**对应于资源名称，可以是你选择的任何内容。  
    * 选择要将 ASN 关联到的**订阅**。
    * **对等名称**对应于你公司的名称，需要尽可能接近你的 PeeringDB 配置文件。 请注意，该值仅支持字符 a-z、a-z 和空格
    * 在 "**对等 asn** " 字段中输入你的 ASN。
    * 单击 "**新建**"，并输入网络运营中心（NOC）的**电子邮件地址**和**电话号码**
1. 然后，单击 "**查看" + "创建**"，然后观察到门户对输入的信息运行基本验证。 这会显示在顶部的功能区中，如*运行最终验证 ...* 。

    > [!div class="mx-imgBorder"]
    > ![PeerAsn 审阅 "选项卡](./media/peerasn-review-tab-validation.png)

1. 功能区中的消息启用*验证*后，请通过单击 "**创建**" 来验证你的信息并提交请求。 如果验证未通过，则单击 "上**一步**"，然后重复上述步骤来修改请求并确保输入的值没有错误。

    > [!div class="mx-imgBorder"]
    > ![PeerAsn 审阅 "选项卡](./media/peerasn-review-tab.png)

1. 提交请求后，请等待它完成部署。 如果部署失败，请联系[Microsoft 对等互连](mailto:peering@microsoft.com)。 成功的部署将如下所示。

    > [!div class="mx-imgBorder"]
    > ![PeerAsn 成功](./media/peerasn-success.png)

### <a name="view-status-of-a-peerasn"></a>查看 PeerAsn 的状态
成功部署 PeerAsn 资源后，需要等待 Microsoft 批准关联请求。 批准最多可能需要12小时。 批准后，你将收到上一节中输入的电子邮件地址的通知。

> [!IMPORTANT]
> 等待 ValidationState 在提交对等互连请求之前变成 "已批准"。 此批准最多可能需要12小时。

## <a name="modify-peerasn"></a>修改 PeerAsn
当前不支持修改 PeerAsn。 如果需要修改，请联系[Microsoft 对等互连](mailto:peering@microsoft.com)。

## <a name="delete-peerasn"></a>删除 PeerAsn
当前不支持删除 PeerAsn。 如果需要删除 PeerAsn，请联系[Microsoft 对等互连](mailto:peering@microsoft.com)。

## <a name="next-steps"></a>后续步骤

* [使用门户创建或修改直接对等互连](howto-direct-portal.md)
* [使用门户将旧直接对等互连转换为 Azure 资源](howto-legacy-direct-portal.md)
* [使用门户创建或修改 Exchange 对等互连](howto-exchange-portal.md)
* [使用门户将旧版 Exchange 对等互连转换为 Azure 资源](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>其他资源

有关详细信息，请访问[Internet 对等互连常见问题解答](faqs.md)