---
title: 网络限制增加 |Microsoft Docs
description: 增加网络限制
author: anavinahar
ms.author: anavin
ms.date: 06/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 48d7e9cc4a3034e149901931f2addbc7df78e2bc
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67297197"
---
# <a name="networking-limit-increase"></a>增加网络限制

若要查看你当前的网络使用情况和配额，可以访问**使用情况 + 配额**Azure 门户边栏选项卡。 此外可以使用使用情况[CLI](https://docs.microsoft.com//cli/azure/network?view=azure-cli-latest#az-network-list-usages)， [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkusage?view=azurermps-6.13.0)或[网络使用情况 API](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/listusage)若要查看你的网络使用情况和限制。

你可以请求通过增加**帮助 + 支持**边栏选项卡或**使用情况 + 配额**门户边栏选项卡。

## <a name="request-networking-quota-increase-at-subscription-level-using-the-help--support-blade"></a>请求在订阅级别使用的网络配额增加**帮助 + 支持**边栏选项卡

请按照下面的说明来创建支持请求通过 Azure 的帮助 + 支持边栏选项卡在 Azure 门户中可用。 

1. 从 https://portal.azure.com ，选择**帮助 + 支持**。

    ![帮助 + 支持](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  选择“新建支持请求”。  

    ![新建支持请求](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. 在问题类型下拉列表中选择**服务和订阅限制 （配额）** 。

    ![问题类型下拉列表](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. 选择需要增加配额的订阅。

    ![选择订阅 newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. 选择**联网**中**配额类型**下拉列表。 

    ![选择配额类型](./media/networking-quota-request/select-quota-type-network.png)

6. 在中**问题详细信息**，通过单击你的请求提供其他信息以帮助进程**提供的详细信息**。

    ![提供详细信息](./media/resource-manager-core-quotas-request/provide-details.png)

7. 在中**配额的详细信息**面板中，选择部署模型、 位置和你想要请求增加的资源。

    ![配额详细信息数据挖掘](./media/networking-quota-request/quota-details-network.png)

8.  输入想对订阅设定的新限制。 若要删除某行，取消选中从资源下拉列表中的资源，或单击"x"丢弃图标。 输入后所需的配额为每个资源，单击**保存并继续**继续创建支持请求配额的详细信息面板上。

    ![新的限制](./media/networking-quota-request/network-new-limits.png)


## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quota-blade"></a>请求在订阅级别使用的网络配额增加**使用情况 + 配额**边栏选项卡

按照下面使用来创建支持请求通过 Azure 的使用情况 + 配额的说明可以在 Azure 门户边栏选项卡。 

1. 从 https://portal.azure.com 中选择“订阅”  。

    ![订阅](./media/resource-manager-core-quotas-request/subscriptions.png)

2. 选择需要增加配额的订阅。

    ![选择订阅](./media/resource-manager-core-quotas-request/select-subscription.png)

3. 选择“使用情况 + 配额” 

    ![选择使用情况和配额](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. 在右上角选择“请求增加”  。

    ![请求增加](./media/resource-manager-core-quotas-request/request-increase.png)

5. 按照从步骤 3 中的步骤*在订阅级别增加请求网络配额*部分使用**帮助 + 支持**边栏选项卡部分

## <a name="about-networking-limits"></a>有关网络限制

若要了解有关网络限制的详细信息，请参阅[网络部分](../azure-subscription-service-limits.md#networking-limits)限制页面或我们的网络限制常见问题解答