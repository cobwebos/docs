---
title: 使用 Azure 门户创建或修改 Exchange 对等互连
titleSuffix: Azure
description: 使用 Azure 门户创建或修改 Exchange 对等互连
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/2/2020
ms.author: derekol
ms.openlocfilehash: 69201c97882846fb929b3b6f9a90be6647603bcc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84700474"
---
# <a name="create-or-modify-an-exchange-peering-by-using-the-azure-portal"></a>使用 Azure 门户创建或修改 Exchange 对等互连

本文介绍如何使用 Azure 门户创建 Microsoft Exchange 对等互连。 本文还介绍如何查看资源状态，以及如何更新、删除和取消预配资源。

如果需要，可以使用 [PowerShell](howto-exchange-powershell.md) 完成本指南。

## <a name="before-you-begin"></a>开始之前
* 在开始配置之前，请查看[先决条件](prerequisites.md)和 [Exchange 对等互连演练](walkthrough-exchange-all.md)。
* 如果已经与 Microsoft 建立了 Exchange 对等互连，但尚未转换为 Azure 资源，请参阅[使用门户将旧版 Exchange 对等互连转换为 Azure 资源](howto-legacy-exchange-portal.md)。

## <a name="create-and-provision-an-exchange-peering"></a>创建和预配 Exchange 对等互连

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>登录到门户，然后选择订阅
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>创建 Exchange 对等互连


作为 Internet Exchange 提供程序，可以通过[创建对等互连]( https://go.microsoft.com/fwlink/?linkid=2129593)来创建 Exchange 对等互连请求。

1. 在“创建对等互连”页面的“基本”选项卡上，按如下所示填写框：

    > [!div class="mx-imgBorder"] 
    > ![注册对等互连服务](./media/setup-basics-tab.png)

*    选择 Azure 订阅。

* 对于“资源组”，可以从下拉列表中选择现有资源组，也可以通过选择“新建”创建新组。 对于本示例，我们将创建新的资源组。

* “名称”对应于资源名称，可以是所选择的任何内容。

* 如果选择现有的资源组，则会自动选择区域。 如果选择创建新的资源组，则还需要选择要在其中驻留资源的 Azure 区域。

>[!NOTE]
>资源组所在的区域独立于要与 Microsoft 创建对等互连的位置。 但是，最佳实践是在驻留在最近的 Azure 区域的资源组内组织对等互连资源。 例如，对于阿什本中的对等互连，可以在美国东部或东部 2 创建资源组。

* 在”PeerASN”框中选择你的 ASN。

>[!IMPORTANT] 
>在提交对等互连请求之前，只能选择 ValidationState 为“已批准”的 ASN。 如果刚刚提交了 PeerAsn 请求，请等待 12 小时，或等待 ASN 关联获得批准。 如果选择的 ASN 正在等待验证，你将看到一条错误消息。 如果看不到需要选择的 ASN，请检查是否选择了正确的订阅。 如果订阅正确，请检查是否已使用**将对等 ASN 关联到 Azure 订阅 [](https://go.microsoft.com/fwlink/?linkid=2129592)** 创建了 PeerAsn。

* 在完成时选择“下一步:**配置以继续**。

#### <a name="configure-connections-and-submit"></a>配置连接并提交
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

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
