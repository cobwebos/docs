---
title: 使用门户的 Exchange 合作伙伴与路由服务器的对等互连连接
titleSuffix: Azure
description: 使用 Azure 门户创建或修改路由服务器的 Exchange 对等互连
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/19/2020
ms.author: derekol
ms.openlocfilehash: 7e10cd91eadd338217845b1504e8e9160bccfc98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91537185"
---
# <a name="create-or-modify-an-exchange-peering-with-route-server-in-azure-portal"></a>在 Azure 门户中使用路由服务器创建或修改 Exchange 对等互连

本文介绍如何使用 Azure 门户创建与路由服务器的 Microsoft Exchange 对等互连。 本文还介绍如何查看资源状态，以及如何更新、删除和取消预配资源。


## <a name="before-you-begin"></a>开始之前
* 在开始配置之前，请查看[先决条件](prerequisites.md)和 [Exchange 对等互连演练](walkthrough-exchange-all.md)。
* 如果已经与 Microsoft 建立了 Exchange 对等互连，但尚未转换为 Azure 资源，请参阅[使用门户将旧版 Exchange 对等互连转换为 Azure 资源](howto-legacy-exchange-portal.md)。

## <a name="create-and-provision-an-exchange-peering"></a>创建和预配 Exchange 对等互连

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>登录到门户，然后选择订阅
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering-with-route-server"></a><a name=create></a>使用路由服务器创建 Exchange 对等互连


作为 Internet Exchange 提供程序，可以通过[创建对等互连]( https://go.microsoft.com/fwlink/?linkid=2129593)来创建 Exchange 对等互连请求。

1. 在“创建对等互连”页面的“基本信息”选项卡上，按如下所示填写相应的框 ：

    > [!div class="mx-imgBorder"] 
    > ![注册对等互连服务](./media/setup-basics-tab.png)

* 选择 Azure 订阅。

* 对于“资源组”，可以从下拉列表中选择现有资源组，也可以通过选择“新建”创建新组。 对于本示例，我们将创建新的资源组。

* “名称”对应于资源名称，可以是所选择的任何内容。

* 如果选择现有的资源组，则会自动选择区域。 如果选择创建新的资源组，则还需要选择要在其中驻留资源的 Azure 区域。

    >[!NOTE]
    >资源组所在的区域独立于要与 Microsoft 创建对等互连的位置。 但是，最佳做法是在位于最近的 Azure 区域的资源组内组织对等互连资源。 例如，对于阿什本中的对等互连，可以在美国东部或东部 2 创建资源组。

* 在”PeerASN”框中选择你的 ASN。

    >[!IMPORTANT] 
    >在提交对等互连请求之前，只能选择 ValidationState 为“已批准”的 ASN。 如果刚刚提交了 PeerAsn 请求，请等待 12 小时，或等待 ASN 关联获得批准。 如果选择的 ASN 正在等待验证，你将看到一条错误消息。 如果看不到需要选择的 ASN，请检查是否选择了正确的订阅。 如果订阅正确，请检查是否已使用[将对等 ASN 关联到 Azure 订阅](https://go.microsoft.com/fwlink/?linkid=2129592)创建了 PeerAsn。

* 在完成时选择“下一步:**配置以继续**。

#### <a name="configure-connections-and-submit"></a>配置连接并提交

1. 在 "创建对等互连" 页上的 "配置" 选项卡上，填写如下所示的框：

    > [!div class="mx-imgBorder"]
    > ![配置路由服务器](./media/setup-exchange-conf-tab-routeserver.png)
 
    * 对于对等类型，请选择 "**直接**"
    * 对于 Microsoft 网络，请选择 " **AS8075" 和 "exchange 路由服务器**"。 
    * 选择 "SKU 为 **基本免费**"。 请勿选择 "高级免费"，因为它是为特殊应用程序保留的。
    * 选择要设置对等互连的 **地铁** 位置。

1. 在 "对**等连接**" 下，选择 "**新建**"

1.  在 " **直接对等连接**" 下，填写以下 BGP 会话详细信息：

    > [!div class="mx-imgBorder"]
    > ![屏幕截图显示添加了详细信息的 "直接对等连接" 窗格。](./media/setup-exchange-conf-tab-direct-route.png)


     * 对等互连设施，为对等互连选择适当的物理位置
     * 会话地址提供程序，选择对等
     * 会话 IPv4 前缀将由 exchange 提供商对等方提供
     * 对于路由服务器，将从其 IP 前缀范围中选择对等会话 IPv4 地址。
     * Microsoft 会话 IPv4 地址，将是从 IP 前缀范围分配的路由器 IP。
     * 会话 IPv6 此时是可选的。
     * 最大播发 IPv4 前缀最高可达20000。 
     * 默认情况下禁用对等互连服务的使用。 一旦 exchange 提供商已使用 Microsoft 签署了对等互连服务协议，就可以启用此功能。

1. 完成后，单击 " **保存**"。 

1. 在 "创建对等互连" 下，会看到通过验证。 验证通过后，单击 "**创建**"

    > [!div class="mx-imgBorder"]
    > ![验证设置](./media/setup-exchange-conf-tab-validation.png)

    >[!NOTE]
    >对于正常的 Internet 服务提供商 (ISP) 为 Microsoft 对等互连服务合作伙伴，需要注册客户 IP 前缀。 但是，对于具有路由服务器的 exchange 合作伙伴，需要注册客户 Asn 而不是前缀。 同一 ASN 密钥对于客户的前缀注册有效。

1. 在 "设置" 部分下选择 " **已注册的 asn** "。

    > [!div class="mx-imgBorder"]
    > ![屏幕截图显示了已注册的一个名为 "Ns" 菜单项的 "对等互连" 窗格。](./media/setup-exchange-registered-asn.png)

1. 选择 " **添加注册的 ASN** "，在 exchange 订阅下创建新的客户 ASN。

    > [!div class="mx-imgBorder"]
    > ![屏幕截图显示了 "注册包含名称和 S 的 S" 窗格。](./media/setup-exchange-register-new-asn.png)

1. 在 "注册 ASN" 下，选择名称，填充客户 ASN，并单击 "保存"。

1. 在 "已注册的 Asn" 下，将有一个分配给每个 ASN 的关联前缀键。 作为 exchange 提供商，你将需要向你的客户提供此前缀密钥，以便他们可以在其订阅下注册对等互连服务。

    > [!div class="mx-imgBorder"]
    > ![屏幕截图显示注册了包含前缀键的的 Ns 窗格。](./media/setup-exchange-register-asn-prefixkey.png)




### <a name="verify-an-exchange-peering"></a><a name=get></a>验证 Exchange 对等互连
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>修改 Exchange 对等互连
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>取消预配 Exchange 对等互连
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>后续步骤

* [使用门户创建或修改直接对等互连](howto-direct-portal.md)
* [使用门户将旧版直接对等互连转换为 Azure 资源](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>其他资源

有关详细信息，请参阅 [Internet 对等互连常见问题解答](faqs.md)。
