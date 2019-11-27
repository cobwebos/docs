---
title: Azure 每个 VM vCPU 配额增加请求 |Microsoft Docs
description: 按 VM vCPU 配额增加请求
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: ccd0c88c95ae9a752ef8ea2387bbde4f8559bc68
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "74531717"
---
# <a name="standard-quota-per-vm-series-vcpu-limit-increase"></a>标准配额：按 VM 序列 vCPU 限制增加

资源管理器支持为虚拟机提供两种类型的 vCPU 配额。 即用**即付 vm 和保留 Vm 实例**使用标准配额。 **低优先级 vm**使用低优先级配额。 对于每个区域中的每个订阅，在两个级别上强制实施即用即付和保留 VM 实例的标准 vCPU 配额

第一层是**区域个 vcpu 限制**（跨所有 VM 系列），第二层是**每个 vm 系列个 vcpu 限制**（例如 Dv3 系列个 vcpu）。 每当部署新 VM 时，该 VM 系列的新的和现有个 vcpu 使用的总和不得超过为该特定 VM 系列批准的 vCPU 配额。 此外，在所有 VM 系列上部署的新的和现有的 vCPU 计数不应超过为订阅批准的总区域个 vcpu 配额。 如果超过了上述任一配额，将不允许部署 VM。
可以从 Azure 门户中为 VM 序列请求增加个 vcpu 配额限制。 VM 序列配额增加会自动增加相同数量的区域个 vcpu 限制。 

在[虚拟机 vCPU 配额页](https://docs.microsoft.com/azure/virtual-machines/windows/quotas)和[Azure 订阅和服务限制页](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests)上了解有关标准 vCPU 配额的详细信息。 

若要详细了解如何为标准配额增加区域 vCPU 限制，请参阅[此处](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests)。 

[在此处](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota)了解有关**增加低优先级 VM vCPU 限制**的详细信息。

可以通过 "**帮助 + 支持**" 边栏选项卡或门户中的 "**使用情况 + 配额**" 边栏选项卡，请求增加**每个 VM 系列的标准 vCPU 配额限制**。

## <a name="request-standard-vcpu-quota-increase-per-vm-series-at-subscription-level-using-the-help--support-blade"></a>使用 "帮助 + 支持" 边栏选项卡，在订阅级别为每个 VM 系列请求标准 vCPU 配额增加

按照下面的说明通过 Azure 的 "帮助 + 支持" 边栏选项卡，在 Azure 门户中创建支持请求。 

你还可以通过单个支持案例请求多个区域的配额。 有关详细信息，请参阅下面的步骤11。

1. 在 https://portal.azure.com中，选择 "**帮助 + 支持**"。

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

7. 在 "**配额详细信息**" 面板中，选择 "**部署模型**" 并选择一个**位置。**

   ![配额详细信息 DM](./media/resource-manager-core-quotas-request/1-7.png)

8. 对于所选位置，选择 "**类型**值" 作为 **"标准"** 。 您可以通过 "**类型**" 字段上的多选支持从单个支持案例请求标准和低优先级配额类型。 详细了解在 **< > "页**上**提高低优先级配额限制**。

   ![SKU 系列](./media/resource-manager-core-quotas-request/1-8.png)

9. 选择需要增加的**SKU 系列**

   ![SKU 系列](./media/resource-manager-core-quotas-request/1-9.png)

10. 输入想对订阅设定的新限制。 若要删除线条，请在 SKU 系列下拉箭头中取消选中 SKU，或者单击 "放弃" x 图标。 

   ![新限制](./media/resource-manager-core-quotas-request/1-10.png)

11. 若要为多个位置请求配额，可以从下拉箭头中检查其他**位置**，并选择适当的 VM 类型。 此步骤预加载针对新位置选择的更早**位置**的 SKU 系列，只需输入所需的新限制即可。

   ![多个位置](./media/resource-manager-core-quotas-request/1-11.png)
   
12. 为每个 SKU 系列输入所需的配额后，在 "配额详细信息" 面板上单击 "**保存并继续**"，以继续创建支持请求。

## <a name="request-standard-vcpu-quota-increase-per-vm-series-at-subscription-level-using-usages--quota-blade"></a>使用 "使用情况 + 配额" 边栏选项卡在订阅级别，请求标准 vCPU 配额增加每个 VM 系列

按照下面的说明操作，通过 Azure 的 "使用情况 + 配额" 边栏选项卡，在 Azure 门户中创建支持请求。

你还可以通过单个支持案例**请求多个区域的配额**。 有关详细信息，请参阅下面的步骤10

1. 从 https://portal.azure.com 中选择“订阅”。

   ![订阅](./media/resource-manager-core-quotas-request/subscriptions.png)

2. 选择需要增加配额的订阅。

   ![选择订阅](./media/resource-manager-core-quotas-request/select-subscription.png)

3. 选择“使用情况 + 配额”

   ![选择使用情况和配额](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. 在右上角选择“请求增加”。

   ![请求增加](./media/resource-manager-core-quotas-request/request-increase.png)

5. 选择 "**计算-VM （个 vcpu）订阅限制**" 将增加为引号类型。 

   ![填写表单](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
6. 在 "**配额详细信息**" 面板中，选择 "部署模型" 并选择一个位置。

   ![“配额问题”边栏选项卡](./media/resource-manager-core-quotas-request/1-1-6.png)

7. 对于所选位置，选择 "**类型**值" 作为 **"标准"** 。 您可以通过 "**类型**" 字段上的多选支持从单个支持案例请求标准和低优先级配额类型。 了解更多有关在此[页](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota)上**提高低优先级个 vcpu 限制**的信息。

   ![选择的 SKU 系列](./media/resource-manager-core-quotas-request/1-1-7.png)
   
   
8. 选择需要增加的**SKU 系列**

   ![选择的 SKU 系列](./media/resource-manager-core-quotas-request/1-1-8.png)

9. 输入想对订阅设定的新限制。 若要删除线条，请在 SKU 系列下拉箭头中取消选中 SKU，或者单击 "放弃" x 图标。 

   ![SKU 新配额请求](./media/resource-manager-core-quotas-request/1-1-9.png)
   

10. 若要为多个位置请求配额，可以从下拉箭头中检查其他**位置**，并选择适当的 VM 类型。 此步骤预加载针对新位置选择的更早**位置**的 SKU 系列，只需输入所需的新限制即可。
   
    ![SKU 新配额请求](./media/resource-manager-core-quotas-request/1-1-10.png)
 
