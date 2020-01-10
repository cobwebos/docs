---
title: 请求增加 Azure 区域 vCPU 配额限制 |Microsoft Docs
description: 区域配额增加请求
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e262651a6e040c40dbe240ad3437eff1914aa3e5
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750842"
---
# <a name="standard-quota-increase-limits-by-region"></a>标准配额：按区域增加限制 

Azure 资源管理器为虚拟机支持两种类型的 vCPU 配额：
* 即*用即付 vm*和*保留 vm 实例*受*标准 vCPU 配额*的限制。
* *污点 vm*服从*点 vCPU 配额*。 

对于每个区域中的每个订阅，在两个级别上强制实施即用即付和保留 VM 实例的标准 vCPU 配额：
* 第一层是*区域个 vcpu 限制*（跨所有 VM 系列）。
* 第二层是*每个 VM 序列个 vcpu 限制*（如 D 系列个 vcpu）。
 
每当部署新的点 VM 时，该 VM 系列的新的和现有 vCPU 使用情况不得超过该特定 VM 系列的已批准 vCPU 配额。 此外，在所有 VM 系列上部署的新的和现有的个 vcpu 的总数不应超过订阅的总批准区域 vCPU 配额。 如果超过了其中任一配额，则不允许进行 VM 部署。 

可以通过使用 Azure 门户来请求提高 VM 系列的 vCPU 配额限制。 VM 序列配额增加会自动增加相同数量的区域 vCPU 限制。

创建新订阅时，默认的区域个 vcpu 总数可能不等于所有单个 VM 序列的总默认 vCPU 配额。 这种差异可能会导致订阅的配额足以满足每个要部署的单个 VM 系列的要求。 但可能没有足够的配额来容纳所有部署的区域个 vcpu。 在这种情况下，必须提交一个请求，以显式增加区域个 vcpu 的总数限制。 区域 vCPU 限制的总量不能超过区域的所有 VM 系列的总批准配额。

若要了解有关标准 vCPU 配额的详细信息，请参阅[虚拟机 vCPU 配额](https://docs.microsoft.com/azure/virtual-machines/windows/quotas)和[Azure 订阅和服务限制](https://aka.ms/quotalimits)。

若要了解有关增加位置 VM vCPU 限制的详细信息，请参阅[污点配额：增加所有 VM 序列的限制](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota)。

可以通过两种方式中的任一方式请求增加 vCPU 标准配额限制，如下一节中所述。

## <a name="request-a-quota-increase-by-region-from-the-help--support-pane"></a>从 "帮助 + 支持" 窗格中请求按区域增加配额

若要从 "**帮助 + 支持**" 窗格中按区域请求 vCPU 配额增加，请执行以下操作： 

1. 在[Azure 门户](https://portal.azure.com)的左窗格中，选择 "**帮助 + 支持**"。

   !["帮助 + 支持" 链接](./media/resource-manager-core-quotas-request/helpsupport.png)
 
1. 在 "**帮助 + 支持**" 窗格中，选择 "**新建支持请求**"。 

    ![新建支持请求](./media/resource-manager-core-quotas-request/newsupportrequest.png)

1. 在 "**问题类型**" 下拉列表中，选择 "**服务和订阅限制（配额）** "。

   !["问题类型" 下拉列表](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

1. 在 "**订阅**" 下拉列表中，选择要增加其配额的订阅。

   !["订阅" 下拉列表](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
1. 在 "**配额类型**" 下拉列表中，选择 "**其他请求**"。

   !["配额类型" 下拉列表](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. 在 "**问题详细信息**" 窗格中，在 "**描述**" 框中提供以下附加信息： 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于**部署模型**，请指定**资源管理器**。  
    b.保留“数据库类型”设置，即设置为“共享”。 对于 "**区域**"，请指定所需的区域（例如 "**美国东部 2**"）。  
    c. 对于 "**新限制**"，请为区域指定新的 vCPU 限制。 此值不应超过此订阅的单个 SKU 系列的已批准配额的总和。

    !["问题详细信息" 窗格](./media/resource-manager-core-quotas-request/regional-details.png)

1. 选择 "**保存并继续**" 继续创建支持请求。

## <a name="request-a-quota-increase-by-region-from-the-subscriptions-pane"></a>从 "订阅" 窗格中请求按区域增加配额

若要从 "**订阅**" 窗格中请求 "区域的 vCPU 配额增加"，请执行以下操作： 

1. 在[Azure 门户](https://portal.azure.com)的左窗格中，选择 "**订阅**"。

   !["订阅" 链接](./media/resource-manager-core-quotas-request/subscriptions.png)

1. 选择要增加其配额的订阅。

   !["订阅" 窗格](./media/resource-manager-core-quotas-request/select-subscription.png)

1. 在 **\<订阅名称 >** "页的左窗格中，选择"**使用情况 + 配额**"。

   !["使用情况 + 配额" 链接](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

1. 在右上角，选择 "**请求增加**"。

   ![请求增加](./media/resource-manager-core-quotas-request/request-increase.png)

1. 在 "**配额类型**" 下拉列表中，选择 "**其他请求**"。

   !["配额类型" 下拉列表](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. 在 "**问题详细信息**" 窗格中，在 "**描述**" 框中提供以下附加信息： 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于**部署模型**，请指定**资源管理器**。  
    b.保留“数据库类型”设置，即设置为“共享”。 对于 "**区域**"，请指定所需的区域（例如 "**美国东部 2**"）。  
    c. 对于 "**新限制**"，请为区域指定新的 vCPU 限制。 此值不应超过此订阅的单个 SKU 系列的已批准配额的总和。

    !["问题详细信息" 窗格](./media/resource-manager-core-quotas-request/regional-details.png)

1. 选择 "**保存并继续**" 继续创建支持请求。

