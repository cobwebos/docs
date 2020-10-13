---
title: 使用 Azure 门户创建或修改直接对等互连
titleSuffix: Azure
description: 使用 Azure 门户创建或修改直接对等互连
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/19/2020
ms.author: derekol
ms.openlocfilehash: 1a89ce873c53e94036aa4f8ac2c2870365924187
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91537202"
---
# <a name="create-or-modify-a-direct-peering-by-using-the-azure-portal"></a>使用 Azure 门户创建或修改直接对等互连

本文介绍如何使用 Azure 门户为 Internet 服务提供商或 Internet Exchange 提供商创建 Microsoft 直接对等互连。 本文还介绍如何查看资源状态，以及如何更新、删除和取消预配资源。

如果需要，可以使用 Azure [PowerShell](howto-direct-powershell.md) 完成本指南。

## <a name="before-you-begin"></a>开始之前
* 在开始配置之前，请查看[先决条件](prerequisites.md)和[直接对等互连演练](walkthrough-direct-all.md)。
* 如果已经与 Microsoft 建立了直接对等互连，但尚未转换为 Azure 资源，请参阅[使用门户将旧版直接对等互连转换为 Azure 资源](howto-legacy-direct-portal.md)。

## <a name="create-and-provision-a-direct-peering"></a>创建和预配直接对等互连

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>登录门户，然后选择订阅
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>创建直接对等互连

作为 Internet 服务提供商或 Internet Exchange 提供商，可以通过[创建对等互连]( https://go.microsoft.com/fwlink/?linkid=2129593)来创建新的直接对等互连请求。

1. 在“创建对等互连”页面的“基本信息”选项卡上，按如下所示填写相应的框 ：


    ![屏幕截图显示 "创建对等互连" 页的 "基本信息" 选项卡，其中包含 "创建新" 链接。](./media/setup-basics-tab.png)

2. 选择 Azure 订阅。

3. 对于“资源组”，可以从下拉列表中选择现有资源组，也可以通过选择“新建”创建新组。 对于本示例，我们将创建新的资源组。

4. “名称”对应于资源名称，可以是所选择的任何内容。

5. 如果选择现有的资源组，则会自动选择区域。 如果选择创建新的资源组，则还需要选择要在其中驻留资源的 Azure 区域。

    >[!NOTE]
    > 资源组所在的区域独立于要与 Microsoft 创建对等互连的位置。 但是，最佳实践是在驻留在最近的 Azure 区域的资源组内组织对等互连资源。 例如，对于阿什本中的对等互连，可以在美国东部或美国东部 2 创建资源组。

6. 在 PeerASN 框中选择你的 ASN。

    >[!IMPORTANT]
    >在提交对等互连请求之前，只能选择 ValidationState 为“已批准”的 ASN。 如果刚刚提交了 PeerAsn 请求，请等待 12 小时，或等待 ASN 关联获得批准。 如果选择的 ASN 正在等待验证，你将看到一条错误消息。 如果看不到需要选择的 ASN，请检查是否选择了正确的订阅。 如果订阅正确，请检查是否已使用[将对等 ASN 关联到 Azure 订阅](https://go.microsoft.com/fwlink/?linkid=2129592)创建了 PeerAsn。

7. 在完成时选择“下一步:配置”以继续。



    ![屏幕截图显示 "创建对等互连" 页的 "基本信息" 选项卡并输入所有值。](./media/setup-direct-basics-filled-tab.png)


#### <a name="configure-connections-and-submit"></a>配置连接并提交
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>验证直接对等互连
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>修改直接对等互连
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>取消预配直接对等互连
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>后续步骤

* [使用门户创建或修改 Exchange 对等互连](howto-exchange-portal.md)
* [使用门户将旧版 Exchange 对等互连转换为 Azure 资源](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>其他资源

有关详细信息，请参阅 [Internet 对等互连常见问题解答](faqs.md)。
