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
ms.openlocfilehash: cee548aff49cd5e4a57eed994b8ade2d157c6313
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75912173"
---
# <a name="associate-peer-asn-to-azure-subscription-using-the-portal"></a>使用门户将对等 ASN 关联到 Azure 订阅

在提交对等请求之前，应首先使用以下步骤将 ASN 与 Azure 订阅相关联。

如果您愿意，您可以使用[PowerShell](howto-subscription-association-powershell.md)完成本指南。

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>创建对等 Asn 以将 ASN 与 Azure 订阅关联

### <a name="sign-in-to-the-portal"></a>登录到门户
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="register-for-peering-resource-provider"></a>注册对等资源提供程序
按照以下步骤注册订阅中的对等资源提供程序。 如果不执行此操作，则无法访问设置对等互连所需的 Azure 资源。

1. 单击门户左上角的**订阅**。 如果您没有看到它，请单击 **"更多服务"** 并搜索它。

    > [!div class="mx-imgBorder"]
    > ![打开订阅](./media/rp-subscriptions-open.png)

1. 单击要用于对等互连的订阅。

    > [!div class="mx-imgBorder"]
    > ![启动订阅](./media/rp-subscriptions-launch.png)

1. 订阅打开后，在左侧，单击**资源提供程序**。 然后，在右侧窗格中，在搜索窗口中搜索*对等互连*，或使用滚动栏查找**Status****Microsoft。** 如果状态为 ***"已注册"，*** 请跳过以下步骤，然后继续创建**对等 Asn**节。 如果状态为 ***"未注册***"，请选择 **"Microsoft.对等"，** 然后单击"**注册**"。

    > [!div class="mx-imgBorder"]
    > ![注册开始](./media/rp-register-start.png)

1. 观察状态更改为***注册***。

    > [!div class="mx-imgBorder"]
    > ![注册正在进行中](./media/rp-register-progress.png)

1. 等待一分钟左右，等待它完成注册。 然后，单击 **"刷新"** 并验证状态是否***已注册***。

    > [!div class="mx-imgBorder"]
    > ![注册完成](./media/rp-register-completed.png)

### <a name="create-peerasn"></a>创建对等 Asn
您可以创建用于将自治系统号 （ASN） 与 Azure 订阅关联的新的 PeerAsn 资源。 您可以通过为需要关联的每个 ASN 创建一个**PeerAsn**将多个 ASN 与订阅相关联。

1. 单击 **"创建资源** > **查看所有**"。

    > [!div class="mx-imgBorder"]
    > ![搜索对等 Asn](./media/peerasn-seeall.png)

1. 在搜索框中搜索*PeerAsn，* 然后按键盘上的 *"输入*"。 从结果中，单击**PeerAsn**资源。

    > [!div class="mx-imgBorder"]
    > ![启动对等 Asn](./media/peerasn-launch.png)

1. 启动**对等操作后**，单击"**创建**"。

    > [!div class="mx-imgBorder"]
    > ![创建对等 Asn](./media/peerasn-create.png)

1. 在 **"关联对等 ASN"** 页上，在 **"基本"** 选项卡下填写字段，如下所示。

    > [!div class="mx-imgBorder"]
    > ![对等 Asn 基础知识选项卡](./media/peerasn-basics-tab.png)

    * **名称**对应于资源名称，可以是您选择的任何名称。  
    * 选择关联 ASN 所需的**订阅**。
    * **对等名称**对应于公司的名称，并且需要尽可能接近您的对等数据库配置文件。 请注意，该值仅支持字符 a-z、A-Z 和空格
    * 在 **"对等 ASN"字段中输入 ASN。**
    * 单击 **"创建新**"并输入网络运营中心 （NOC）**的电子邮件地址**和**电话号码**
1. 然后，单击 **"审阅 + 创建**"，并观察门户对输入的信息进行基本验证。 这显示在顶部的功能区中，如*正在运行最终验证...*

    > [!div class="mx-imgBorder"]
    > ![对等 Asn 审阅选项卡](./media/peerasn-review-tab-validation.png)

1. 功能区中的消息变为 *"验证已通过"* 后，请验证您的信息，并通过单击"**创建**"提交请求。 如果验证未通过，请单击 **"上一步"** 并重复上述步骤以修改请求并确保输入的值没有错误。

    > [!div class="mx-imgBorder"]
    > ![对等 Asn 审阅选项卡](./media/peerasn-review-tab.png)

1. 提交请求后，请等待它完成部署。 如果部署失败，请与[微软对等互连](mailto:peering@microsoft.com)。 成功部署将显示如下。

    > [!div class="mx-imgBorder"]
    > ![同行成功](./media/peerasn-success.png)

### <a name="view-status-of-a-peerasn"></a>查看对等 Asn 的状态
成功部署 PeerAsn 资源后，您需要等待 Microsoft 批准关联请求。 可能需要长达 12 小时才能获得批准。 获得批准后，您将收到上述部分中输入的电子邮件地址的通知。

> [!IMPORTANT]
> 等待验证状态在提交对等请求之前将"已批准"变为"已批准"。 此批准可能需要长达 12 小时。

## <a name="modify-peerasn"></a>修改对等Asn
当前不支持修改对等 Asn。 如果需要修改，请与[Microsoft 对等互连](mailto:peering@microsoft.com)。

## <a name="delete-peerasn"></a>删除对等 Asn
当前不支持删除对等 Asn。 如果需要删除对等器，请与[微软对等互连](mailto:peering@microsoft.com)。

## <a name="next-steps"></a>后续步骤

* [使用门户创建或修改直接对等互连](howto-direct-portal.md)
* [使用门户将旧版直接对等互连转换为 Azure 资源](howto-legacy-direct-portal.md)
* [使用门户创建或修改 Exchange 对等互连](howto-exchange-portal.md)
* [使用门户将旧版 Exchange 对等互连转换为 Azure 资源](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>其他资源

有关详细信息，请访问[互联网对等常见问题解答](faqs.md)