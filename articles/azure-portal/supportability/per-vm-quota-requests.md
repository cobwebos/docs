---
title: 请求增加每个 Azure VM 系列的 vCPU 配额限制 |Microsoft Docs
description: 本文讨论如何按 VM vCPU 请求增加配额限制。
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: fc59ffda245834c716067bc63e6c3745fa5d23b3
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897242"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>标准配额：按 VM 序列增加限制

Azure 资源管理器为虚拟机支持两种类型的 vCPU 配额：
* 即*用即付 vm*和*保留 vm 实例*受*标准 vCPU 配额*的限制。
* *污点 vm*服从*点 vCPU 配额*。 

对于每个区域中的每个订阅，在两个级别上强制实施即用即付和保留 VM 实例的标准 vCPU 配额：
* 第一层是*区域个 vcpu 限制*（跨所有 VM 系列）。
* 第二层是*每个 VM 序列个 vcpu 限制*（如 Dv3 系列个 vcpu）。 

每当部署新的点 VM 时，该 VM 系列的新的和现有 vCPU 使用情况不得超过该特定 VM 系列的已批准 vCPU 配额。 此外，在所有 VM 系列上部署的新的和现有的个 vcpu 的总数不应超过订阅的总批准区域 vCPU 配额。 如果超过了其中任一配额，则不允许进行 VM 部署。

可以通过使用 Azure 门户来请求提高 VM 系列的 vCPU 配额限制。 VM 序列配额增加会自动增加相同数量的区域 vCPU 限制。 

若要了解有关标准 vCPU 配额的详细信息，请参阅[虚拟机 vCPU 配额](https://docs.microsoft.com/azure/virtual-machines/windows/quotas)和[Azure 订阅和服务限制](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests)。 

若要了解有关为标准配额增加区域的 vCPU 限制的信息，请参阅[标准配额：按区域增加限制](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests)。 

若要了解有关增加位置 VM vCPU 限制的详细信息，请参阅[污点配额：增加所有 VM 序列的限制](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota)。

可以通过以下两种方式之一请求提高每个 VM 系列的标准 vCPU 配额限制，如以下部分中所述。

## <a name="request-a-standard-quota-increase-from-the-help--support-pane"></a>从 "帮助 + 支持" 窗格中请求标准配额增加

若要从 "**帮助 + 支持**" 窗格中请求每个 VM 系列的标准 vCPU 配额增加，请执行以下操作： 

> [!NOTE]
> 你还可以通过单个支持案例为多个区域请求增加配额限制。 有关详细信息，请参阅步骤8。

1. 在[Azure 门户](https://portal.azure.com)的左窗格中，选择 "**帮助 + 支持**"。

   !["帮助 + 支持" 链接](./media/resource-manager-core-quotas-request/helpsupport.png)
 
1. 在 "**帮助 + 支持**" 窗格中，选择 "**新建支持请求**"。 

    ![新建支持请求](./media/resource-manager-core-quotas-request/newsupportrequest.png)

1. 在 "**问题类型**" 下拉列表中，选择 "**服务和订阅限制（配额）** "。

   !["问题类型" 下拉列表](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

1. 在 "**订阅**" 下拉列表中，选择要增加其配额的订阅。

   !["订阅" 下拉列表](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
1. 在 "**配额类型**" 下拉列表中，选择 "**计算-VM （个 vcpu）订阅限制增加**"。 

   !["配额类型" 下拉列表](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. 在 "**详细**信息" 选项卡上的 "**问题详细信息**" 下，选择 "**提供详细**信息"，然后输入附加信息以帮助处理您的请求。

   !["提供详细信息" 链接](./media/resource-manager-core-quotas-request/provide-details.png)

1. 在右上角的 "**配额详细信息**" 窗格中，执行以下操作：

   !["配额详细信息" 窗格](./media/resource-manager-core-quotas-request/1-7.png)

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在 "**部署模型**" 下拉列表中，选择相应的模型。

   b.保留“数据库类型”设置，即设置为“共享”。 在 "**位置**" 下拉列表中，选择一个位置。 对于所选位置，在 "**类型**" 下的 "**选择类型**" 框中，输入 "**标准**"。

   !["配额详细信息" 窗格-配额类型](./media/resource-manager-core-quotas-request/1-8.png)

   在 "**类型**" 下，你可以通过多选支持从单个支持案例请求标准和点配额类型。
   
   有关增加点配额限制的详细信息，请参阅[用于虚拟机规模集的 Azure 点 vm](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot)。

   c. 在**标准**下拉列表下方，选择要增加其配额的 SKU 系列。

   !["配额详细信息" 窗格-SKU 系列](./media/resource-manager-core-quotas-request/1-9.png)

   d.单击“下一步”。 输入要用于此订阅的新配额限制。 若要从列表中删除 SKU，请清除 SKU 旁边的复选框，或选择 "**删除**" （X）图标。 

   !["New vCPU Limit" 文本框](./media/resource-manager-core-quotas-request/1-10.png)

1. 若要为多个位置请求增加配额，请在下拉列表中选择其他位置，然后选择适当的 VM 类型。 然后，可以输入应用于其他位置的限制。

   !["配额详细信息" 窗格中的其他位置](./media/resource-manager-core-quotas-request/1-11.png)
   
1. 选择 "**保存并继续**" 继续创建支持请求。

## <a name="request-a-standard-quota-increase-from-the-subscriptions-pane"></a>从 "订阅" 窗格中请求标准配额增加

若要从 "**订阅**" 窗格中请求每个 VM 系列的标准 vCPU 配额增加，请执行以下操作：

> [!NOTE]
> 你还可以通过单个支持案例为多个区域请求增加配额限制。 有关详细信息，请参阅步骤7。

1. 在[Azure 门户](https://portal.azure.com)的左窗格中，选择 "**订阅**"。

   !["订阅" 链接](./media/resource-manager-core-quotas-request/subscriptions.png)

1. 选择要增加其配额的订阅。

   !["订阅" 窗格](./media/resource-manager-core-quotas-request/select-subscription.png)

1. 在 **\<订阅名称 >** "页的左窗格中，选择"**使用情况 + 配额**"。

   !["使用情况 + 配额" 链接](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

1. 在右上角，选择 "**请求增加**"。

   ![请求增加](./media/resource-manager-core-quotas-request/request-increase.png)

1. 在 "**配额类型**" 下拉列表中，选择 "**计算-VM （个 vcpu）订阅限制增加**"。

   !["配额类型" 下拉列表](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
1. 在右上角的 "**配额详细信息**" 窗格中，执行以下操作：

   !["配额详细信息" 窗格](./media/resource-manager-core-quotas-request/1-1-6.png)

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在 "**部署模型**" 下拉列表中，选择相应的模型。

   b.保留“数据库类型”设置，即设置为“共享”。 在 "**位置**" 下拉列表中，选择一个位置。 
   
   c. 对于所选位置，请在 "**类型**" 下选择 "**选择类型**"，然后选中 "**标准**" 复选框。

   !["标准" 复选框](./media/resource-manager-core-quotas-request/1-1-7.png)
   
   在 "**类型**" 下，你可以通过多选支持从单个支持案例请求标准和低优先级配额类型。
   
   有关增加点配额限制的详细信息，请参阅[用于虚拟机规模集的 Azure 点 vm](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot)。

   d.单击“下一步”。 在**标准**下拉列表下方，选择要增加其配额的 SKU 系列。

   !["配额详细信息" 窗格-SKU 系列](./media/resource-manager-core-quotas-request/1-1-8.png)

   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 输入要用于此订阅的新配额限制。 若要从列表中删除 SKU，请清除 SKU 旁边的复选框或选择 "**删除**" （X）。 

   !["New vCPU Limit" 文本框](./media/resource-manager-core-quotas-request/1-1-9.png)
   
1. 若要为多个位置请求增加配额，请在下拉列表中选择其他位置，然后选择适当的 VM 类型。 

   此步骤预加载您为之前的位置选择的 SKU 系列。 输入要应用于其他序列的配额限制。
   
   !["配额详细信息" 窗格中的其他位置](./media/resource-manager-core-quotas-request/1-1-10.png)
 
1. 选择 "**保存并继续**" 继续创建支持请求。
