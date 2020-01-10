---
title: 点虚拟机配额 |Microsoft Docs
description: 通过发出污点配额请求提高配额限制
author: sowmyavenkat86
ms.author: svenkat
ms.date: 11/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: b087315e02a61886fa6f4ef083c75ed6b5b60cf9
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750321"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>污点配额：增加所有 VM 序列的限制

点虚拟机（Vm）提供了不同的 Azure 使用模型。 它们可让你在 exchange 中承担更低的成本，让 Azure 根据即用即付或保留 VM 实例部署需要删除 Vm。 有关污点 Vm 的详细信息，请参阅[用于虚拟机规模集的 Azure 点 vm](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot)。

Azure 资源管理器为虚拟机支持两种类型的 vCPU 配额：
* 即*用即付 vm*和*保留 vm 实例*受*标准 vCPU 配额*的限制。
* *污点 vm*服从*点 vCPU 配额*。 

对于 "*污点 vCPU 配额*类型"，将在所有可用的 VM 系列上强制实施资源管理器 vCPU 配额，作为单个区域限制。

每当部署新的点 VM 时，所有查找点 VM 实例的新的和现有 vCPU 使用情况不得超过已批准的点 vCPU 配额限制。 如果超出了污点配额，则不允许使用点 VM 部署。 

本文介绍如何使用 Azure 门户请求增加位置 vCPU 配额限制。 

若要了解有关标准 vCPU 配额的详细信息，请参阅[虚拟机 vCPU 配额](https://docs.microsoft.com/azure/virtual-machines/windows/quotas)和[Azure 订阅和服务限制](https://aka.ms/quotalimits)。 

若要了解有关按区域增加 vCPU 限制的信息，请参阅[标准配额：区域 vCPU 限制增加](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests)。

## <a name="request-a-quota-limit-increase-from-the-help--support-pane"></a>从 "帮助 + 支持" 窗格中请求增加配额限制 

若要从 "**帮助 + 支持**" 窗格中为所有 VM 序列请求一个点配额限制增加，请执行以下操作：

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

1. 选择 "**详细**信息" 选项卡，然后在 "**问题详细信息**" 下，选择 "**提供详细**信息"，然后输入附加信息以帮助处理您的请求。

   !["提供详细信息" 链接](./media/resource-manager-core-quotas-request/provide-details.png)
   
1. 在右上角的 "**配额详细信息**" 窗格中，执行以下操作：

   !["配额详细信息" 窗格](./media/resource-manager-core-quotas-request/3-7.png)

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在 "**部署模型**" 下拉列表中，选择相应的模型。

   b.保留“数据库类型”设置，即设置为“共享”。 在 "**位置**" 下拉列表中，选择一个位置。 对于所选位置，请在 "**类型**" 下的 "**选择类型**" 框中输入 "**点**"。 
   
   !["新建支持请求" 详细信息选项卡](./media/resource-manager-core-quotas-request/3-8.png)

    在 "**类型**" 下，你可以通过多选支持从单个支持案例请求标准和点配额类型。 
    
    有关详细信息，请参阅[标准配额：按 VM 序列 vCPU 限制增加](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests)。

   c. 输入要用于此订阅的新配额限制。 
 
   !["New vCPU Limit" 文本框](./media/resource-manager-core-quotas-request/3-9.png)

1. 若要为多个位置请求增加配额，请在下拉列表中选择其他位置，然后选择适当的 VM 类型。 然后，可以输入应用于其他位置的限制。

   !["配额详细信息" 窗格中的其他位置](./media/resource-manager-core-quotas-request/3-10.png)

1. 选择 "**保存并继续**" 继续创建支持请求。

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>从 "订阅" 窗格中请求增加配额限制

若要从 "**订阅**" 窗格中为所有 VM 序列请求一个点配额限制增加，请执行以下操作：

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

   !["配额详细信息" 窗格](./media/resource-manager-core-quotas-request/3-2-6.png)
 
   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在 "**部署模型**" 下拉列表中，选择相应的模型。

   b.保留“数据库类型”设置，即设置为“共享”。 在 "**位置**" 下拉列表中，选择一个位置。 
   
   c. 对于所选位置，请在 "**类型**" 下的 "**选择类型**" 框中输入 "**点**"。

   !["配额详细信息" 窗格](./media/resource-manager-core-quotas-request/3-2-7.png)

   有关详细信息，请参阅[标准配额：按 VM 序列 vCPU 限制增加](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests)。

   d.单击“下一步”。 输入要用于此订阅的新配额限制。

   !["New vCPU Limit" 文本框](./media/resource-manager-core-quotas-request/3-2-8.png)
 
1. 若要为多个位置请求增加配额，请在下拉列表中选择其他位置，然后选择适当的 VM 类型。 然后，可以输入应用于其他位置的限制。

   !["配额详细信息" 窗格中的其他位置](./media/resource-manager-core-quotas-request/3-2-9.png)
 
1. 选择 "**保存并继续**" 继续创建支持请求。


