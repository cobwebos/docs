---
title: 污点配额 |Microsoft Docs
description: 污点配额请求
author: sowmyavenkat86
ms.author: svenkat
ms.date: 11/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 09c9d7940314b691e6351353e6a0076510fdcb13
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850561"
---
# <a name="spot-quota-limit-increase-for-all-vm-series"></a>点配额：限制所有 VM 序列的增加

污点 Vm 提供不同的 Azure 使用模型，因此，根据即用即付或保留 VM 实例部署的需要，为 Azure 删除 VM 所需的成本更低。 [在此处](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot)了解有关点 vm 的详细信息。

资源管理器支持为虚拟机提供两种类型的 vCPU 配额。 即用**即付 vm 和保留 Vm 实例**使用标准配额。 **污点 vm**使用点配额。 

对于 "**点配额**类型"，将在所有可用的 VM 系列上强制实施资源管理器 vCPU 配额，作为单个区域限制。

每当要部署新的专色 VM 时，所有查找点 VM 实例的新的和现有个 vcpu 使用的总和不得超过已批准的点 vCPU 配额限制。 如果超出了污点配额，则将不允许点 VM 部署。 可以从 Azure 门户中请求增加位置个 vcpu 配额限制。 

在虚拟机 vCPU 配额页和 Azure 订阅和服务限制页上了解有关标准 vCPU 配额的详细信息。 详细了解如何增加此[页](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests)上的标准配额的区域 vCPU 限制。

你现在可以通过 "**帮助 + 支持**" 边栏选项卡或门户中的 "**使用情况 + 配额**" 边栏选项卡**为所有 VM 系列请求增加点配额限制**。

## <a name="request-spot-quota-limit-increase-for-all-vm-series-per-subscription-using-the-help--support-blade"></a>使用 "帮助 + 支持" 边栏选项卡为每个订阅的每个订阅的所有 VM 序列增加请求点配额限制

按照下面的说明通过 Azure 的 "帮助 + 支持" 边栏选项卡，在 Azure 门户中创建支持请求。

你还可以通过单个支持案例**请求多个区域的配额**。 有关详细信息，请参阅下面的步骤10。 


1. 在 https://portal.azure.com 中，选择 "**帮助 + 支持**"。

   ![帮助 + 支持](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  选择“新建支持请求”。 

     ![新建支持请求](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. 在 "问题类型" 下拉菜单中，选择 "**服务和订阅限制（配额）** "。

   !["问题类型" 下拉](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. 选择需要增加配额的订阅。

   ![选择订阅 newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. 选择 "**计算-VM （个 vcpu）订阅限制增加** **配额类型**" 下拉。 

   ![选择配额类型](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. 在 "**问题详细信息**" 中，通过单击 "**提供详细信息**" 提供附加信息以帮助处理您的请求。

   ![提供详细信息](./media/resource-manager-core-quotas-request/provide-details.png)
   
7.  在 "**配额详细信息**" 面板中，选择 "**部署模型**" 并选择一个**位置**。

![提供详细信息](./media/resource-manager-core-quotas-request/3-7.png)

8. 对于所选位置，选择 "**类型**值" 作为 **"点"** 。 您可以通过 "**类型**" 字段上的多选支持从单个支持案例请求标准和点配额类型。 在此[页](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests)上了解有关**增加每个 VM 系列的标准配额**的详细信息。

![提供详细信息](./media/resource-manager-core-quotas-request/3-8.png)

9.  输入订阅上所需的新限制。 
 
 ![提供详细信息](./media/resource-manager-core-quotas-request/3-9.png)

10. 若要为多个位置请求配额，可以从下拉箭头中检查其他位置，并选择适当的 VM 类型。 然后，可以输入所需的新限制。

![提供详细信息](./media/resource-manager-core-quotas-request/3-10.png)

11. 输入所需的配额后，在 "配额详细信息" 面板上单击 "**保存并继续**"，以继续创建支持请求。

## <a name="request-spot-quota-limit-increase-for-all-vm-series-per-subscription-using-usages--quota-blade"></a>使用使用情况 + 配额边栏选项卡为每个订阅的每个订阅的所有 VM 序列增加请求点配额限制

按照下面的说明操作，通过 Azure 的 "使用情况 + 配额" 边栏选项卡，在 Azure 门户中创建支持请求。

你还可以通过单个支持案例**请求多个区域的配额**。 有关详细信息，请参阅下面的步骤9。 

1.  从 https://portal.azure.com 中选择“订阅”。

 ![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2.  选择需要增加配额的订阅。

 ![选择订阅](./media/resource-manager-core-quotas-request/select-subscription.png)

3.  选择“使用情况 + 配额”。

 ![选择使用情况和配额](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4.  在右上角，选择 "**请求增加"。**

   ![请求增加](./media/resource-manager-core-quotas-request/request-increase.png)

5.  选择 "**计算-VM （个 vcpu）订阅限制**" 将增加为引号类型。

  ![填写表单](./media/resource-manager-core-quotas-request/select-quota-type.png)

6.  在 "**配额详细信息**" 面板中，选择 "部署模型" 并选择一个位置。

  ![填写表单](./media/resource-manager-core-quotas-request/3-2-6.png)
 
7.  对于所选位置，选择 "**类型**值" 作为 **"点"。** 您可以通过 "**类型**" 字段上的多选支持从单个支持案例请求标准和点配额类型。 在此[页](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests)上了解有关**增加每个 VM 系列的标准配额**的详细信息。

  ![填写表单](./media/resource-manager-core-quotas-request/3-2-7.png)
 
8.  输入订阅上所需的新限制。

  ![填写表单](./media/resource-manager-core-quotas-request/3-2-8.png)
 
9.  若要为多个位置请求配额，可以从下拉箭头中检查其他**位置**，并选择适当的 VM 类型。 然后，可以输入所需的新限制。

  ![填写表单](./media/resource-manager-core-quotas-request/3-2-9.png)
 
10. 输入所需的配额后，在 "配额详细信息" 面板上单击 "**保存并继续**"，以继续创建支持请求。


