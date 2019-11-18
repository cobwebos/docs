---
title: 增加了网络限制 |Microsoft Docs
description: 网络限制提高
author: anavinahar
ms.author: anavin
ms.date: 06/19/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 5cadd0479a6ddc97cc7ee7d9b5aee1bbffba02bb
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74120964"
---
# <a name="networking-limit-increase"></a>网络限制提高

若要查看当前的网络使用情况和配额，可以访问 Azure 门户中的 "**使用情况 + 配额**" 边栏选项卡。 你还可以使用使用情况[CLI](https://docs.microsoft.com//cli/azure/network?view=azure-cli-latest#az-network-list-usages)、 [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkusage?view=azurermps-6.13.0)或[网络使用情况 API](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/listusage)来查看网络使用情况和限制。

可以通过 "**帮助 + 支持**" 边栏选项卡或门户中的 "**使用情况 + 配额**" 边栏选项卡请求增加。

> [!Note]
> 若要更改公共 IP 前缀的默认大小，请从下拉列表中选择 "最小公共 IP 网际网络前缀长度"。

## <a name="request-networking-quota-increase-at-subscription-level-using-the-help--support-blade"></a>使用 "**帮助 + 支持**" 边栏选项卡在订阅级别增加请求网络配额

按照下面的说明通过 Azure 的 "帮助 + 支持" 边栏选项卡，在 Azure 门户中创建支持请求。 

1. 在 https://portal.azure.com中，选择 "**帮助 + 支持**"。

    ![帮助 + 支持](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  选择“新建支持请求”。 

    ![新建支持请求](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. 在 "问题类型" 下拉列表中，选择 "**服务和订阅限制（配额）** "。

    ![问题类型下拉列表](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. 选择需要增加配额的订阅。

    ![选择订阅 newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. 在 "**配额类型**" 下拉列表中选择**网络**。 

    ![选择配额类型](./media/networking-quota-request/select-quota-type-network.png)

6. 在 "**问题详细信息**" 中，通过单击 "**提供详细信息**" 提供附加信息以帮助处理您的请求。

    ![提供详细信息](./media/resource-manager-core-quotas-request/provide-details.png)

7. 在 "**配额详细信息**" 面板中，选择 "部署模型"、要请求增加的位置和资源。

    ![配额详细信息 DM](./media/networking-quota-request/quota-details-network.png)

8.  输入想对订阅设定的新限制。 若要删除行，请从 "资源" 下拉列表中取消选中资源，或单击 "放弃 ' x" 图标。 为每个资源输入所需的配额后，在 "配额详细信息" 面板上单击 "**保存并继续**"，以继续创建支持请求。

    ![新限制](./media/networking-quota-request/network-new-limits.png)


## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quota-blade"></a>使用 "使用情况 **+ 配额**" 边栏选项卡在订阅级别增加请求网络配额

按照下面的说明操作，通过 Azure 的 "使用情况 + 配额" 边栏选项卡，在 Azure 门户中创建支持请求。 

1. 从 https://portal.azure.com 中选择“订阅”。

    ![订阅](./media/resource-manager-core-quotas-request/subscriptions.png)

2. 选择需要增加配额的订阅。

    ![选择订阅](./media/resource-manager-core-quotas-request/select-subscription.png)

3. 选择“使用情况 + 配额”

    ![选择使用情况和配额](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. 在右上角选择“请求增加”。

    ![请求增加](./media/resource-manager-core-quotas-request/request-increase.png)

5. 使用 "**帮助 + 支持**" 边栏选项卡上的 "*请求网络配额增加*" 部分中的步骤3开始执行步骤

## <a name="about-networking-limits"></a>关于网络限制

若要了解有关网络限制的详细信息，请参阅 "限制" 页的 "[网络" 部分](../azure-subscription-service-limits.md#networking-limits)或我们的网络限制常见问题
