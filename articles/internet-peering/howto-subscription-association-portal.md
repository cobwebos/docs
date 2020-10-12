---
title: 使用门户将对等 ASN 关联到 Azure 订阅
titleSuffix: Azure
description: 使用门户将对等 ASN 关联到 Azure 订阅
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/18/2020
ms.author: derekol
ms.openlocfilehash: 22cb179925f95fd0762371cc904fcbd02675339a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540262"
---
# <a name="associate-peer-asn-to-azure-subscription-using-the-portal"></a>使用门户将对等 ASN 关联到 Azure 订阅

作为 Internet 服务提供商或 Internet Exchange 提供商，在提交对等互连请求之前，你应该首先使用以下步骤将你的 ASN 与 Azure 订阅相关联。

如果需要，可以使用 [PowerShell](howto-subscription-association-powershell.md) 完成本指南。

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>创建 PeerAsn 以将 ASN 与 Azure 订阅关联

### <a name="sign-in-to-the-portal"></a>登录门户
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="register-for-peering-resource-provider"></a>注册对等互连资源提供程序
按照以下步骤，在订阅中注册对等互连资源提供程序。 如果未执行此操作，则设置对等互连所需的 Azure 资源将无法访问。

1. 单击门户左上角的“订阅”。 如果看不到它，请单击“更多服务”并进行搜索。

    > [!div class="mx-imgBorder"]
    > ![打开订阅](./media/rp-subscriptions-open.png)

1. 单击要用于对等互连的订阅。

    > [!div class="mx-imgBorder"]
    > ![订阅](./media/rp-subscriptions-launch.png)

1. 订阅打开后，在左侧单击“资源提供程序”。 然后，在右窗格中，在搜索窗口中搜索“peering”，或使用滚动条查找 Microsoft.Peering，并查看“状态”。 如果状态为“已注册”，请跳过以下步骤，然后继续到“创建 PeerAsn”部分。 如果状态为“未注册”，请选择“Microsoft.Peering”，然后单击“注册”。

    > [!div class="mx-imgBorder"]
    > ![注册开始](./media/rp-register-start.png)

1. 观察到状态更改为“正在注册”。

    > [!div class="mx-imgBorder"]
    > ![注册正在进行](./media/rp-register-progress.png)

1. 等待一分钟左右，使它可完成注册。 然后，单击“刷新”并验证状态是否为“已注册”。

    > [!div class="mx-imgBorder"]
    > ![注册完成](./media/rp-register-completed.png)

### <a name="create-peerasn"></a>创建 PeerAsn
作为 Internet 服务提供商或 Internet Exchange 提供商，你可以创建新的 PeerAsn 资源，以便在 " [关联对等 ASN" 页](https://go.microsoft.com/fwlink/?linkid=2129592) 上将自治系统编号 (ASN) 与 Azure 订阅相关联。 可以通过为需要关联的每个 ASN 创建 **PeerAsn** ，将多个 asn 关联到一个订阅。

1. 在“关联对等 ASN”页面上的“基本信息”选项卡下，按如下所示填写字段。

    > [!div class="mx-imgBorder"]
    > ![PeerAsn 基本信息选项卡](./media/peerasn-basics-tab.png)

    * “名称”对应于资源名称，可以是所选择的任何内容。  
    * 选择需要将 ASN 与之关联的“订阅”。
    * “对等名称”对应于公司名称，需要尽可能与 PeeringDB 配置文件接近。 请注意，该值仅支持字符 a-z、A-Z 和空格
    * 在“对等 ASN”字段中输入你的 ASN。
    * 单击“新建”，为网络运营中心 (NOC) 输入“电子邮件地址”和“电话号码”
1. 然后，单击“审阅 + 创建”，会观察到门户对所输入的信息运行基本验证。 这会在顶部的功能区中显示为“正在运行最终验证...”。

    > [!div class="mx-imgBorder"]
    > ![屏幕截图显示关联对等方 A S N 基本信息选项卡。](./media/peerasn-review-tab-validation.png)

1. 功能区中的消息变为“通过验证”后，通过单击“创建”来验证信息并提交请求。 如果验证未通过，则单击“上一步”，然后重复以上步骤来修改请求并确保输入的值没有错误。

    > [!div class="mx-imgBorder"]
    > ![屏幕截图显示了如何关联对等方 A S N 基本信息选项卡，并通过验证。](./media/peerasn-review-tab.png)

1. 提交请求之后，请等待它完成部署。 如果部署失败，请联系 [Microsoft 对等互连](mailto:peering@microsoft.com)。 成功的部署将如下所示。

    > [!div class="mx-imgBorder"]
    > ![PeerAsn 成功](./media/peerasn-success.png)

### <a name="view-status-of-a-peerasn"></a>查看 PeerAsn 的状态
成功部署 PeerAsn 资源后，需要等待 Microsoft 批准关联请求。 批准最多可能需要 12 小时。 批准后，你将在上一部分中输入的电子邮件地址处收到通知。

> [!IMPORTANT]
> 在提交对等互连请求之前，等待 ValidationState 变成“Approved”。 此批准最多可能需要 12 小时。

## <a name="modify-peerasn"></a>修改 PeerAsn
当前不支持修改 PeerAsn。 如果需要修改，请联系 [Microsoft 对等互连](mailto:peering@microsoft.com)。

## <a name="delete-peerasn"></a>删除 PeerAsn
当前不支持删除 PeerAsn。 如果需要删除 PeerAsn，请联系 [Microsoft 对等互连](mailto:peering@microsoft.com)。

## <a name="next-steps"></a>后续步骤

* [使用门户创建或修改直接对等互连](howto-direct-portal.md)
* [使用门户将旧版直接对等互连转换为 Azure 资源](howto-legacy-direct-portal.md)
* [使用门户创建或修改 Exchange 对等互连](howto-exchange-portal.md)
* [使用门户将旧版 Exchange 对等互连转换为 Azure 资源](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>其他资源

有关详细信息，请访问 [Internet 对等互连常见问题解答](faqs.md)
