---
title: "在本教程中，你创建 Azure 堆栈提供 |Microsoft 文档"
description: "了解如何创建 Azure 堆栈产品包括计划和配额。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 083b5e20b89f22cb8e523926858fe9ffb1441319
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-offer-azure-stack-iaas-services"></a>教程： 提供 Azure 堆栈 IaaS 服务
Azure Stack 云管理员可以创建产品供用户（有时称为租户）订阅。 然后，用户可以通过其订阅来使用 Azure Stack 服务。

本教程演示如何创建提议，以便用户能够创建基于中上载的 Azure 堆栈应用商店 Windows Server 2016 Datacenter 映像的虚拟机[以前一教程](asdk-marketplace-item.md)。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建产品
> * 创建计划
> * 设置配额
> * 为公共的组产品/服务

在 Azure Stack 中，可通过订阅、产品和计划将服务交付给用户。 用户可以订阅多个产品。 提供可以有一个或多个计划，并计划可以有一个或多个服务，如下面的关系图中所示：

![订阅、产品和计划](media/asdk-offer-services/sop.png)

作为 Azure 堆栈操作员提供服务，你首先系统提示创建产品/服务，则某一计划、 和最后的配额。 创建提议后，Azure 堆栈用户然后可以订阅产品/服务通过用户门户。

## <a name="create-an-offer"></a>创建产品
**提供**是组的一个或多个 Azure 堆栈运算符显示给用户购买或订阅的计划。

1. 登录到[Azure 堆栈门户](https://adminportal.local.azurestack.external)作为云管理员联系。

2. 单击**新** > **提供 + 计划** > **提供**。

   ![新产品/服务](media/asdk-offer-services/new-offer.png)

2. 在“新建产品”部分填写“显示名称”和“资源名称”，然后选择一个新的或现有的**资源组**。 显示名称是用户看到产品/服务的公共友好名称。 仅云操作员可以查看管理员用于为 Azure 资源管理器资源提供工作的资源名称。

   ![显示名称](media/asdk-offer-services/offer-display-name.png)


## <a name="create-a-plan"></a>创建计划
输入基本优惠信息后，必须将至少一个基本计划添加到产品/服务中。 

**计划**允许 Azure 堆栈操作员组服务和其关联的配额，向用户提供。

1. 单击“基本计划”，在“计划”部分单击“添加”，将新计划添加到产品。

   ![添加计划](media/asdk-offer-services/new-plan.png)

2. 在“新建计划”部分填写“显示名称”和“资源名称”。 显示名称是用户看到的计划的友好名称。 仅云操作员可以查看云运算符用于使用 Azure 资源管理器资源作为计划的资源名称。

   ![计划显示名称](media/asdk-offer-services/plan-display-name.png)

3. 接下来，选择要在计划中包含的服务。 若要提供 IaaS 服务，请单击**服务**，选择**Microsoft.Compute**， **Microsoft.Network**，和**Microsoft.Storage**，，然后单击**选择**。

   ![计划服务](media/asdk-offer-services/select-services.png)


## <a name="set-quotas"></a>设置配额
现在，此项优惠活动具有一个计划，包括服务，你必须为每个设置配额。 

**配额**确定用户可以使用为每个服务是否正在提供的计划中包含的资源量。

1. 单击**配额**，然后选择你想要创建的配额的服务。 

   若要开始，首先创建计算服务的配额。 在命名空间列表中，选择“Microsoft.Compute”命名空间，然后单击“创建新配额”。
   
   ![创建新配额](media/asdk-offer-services/create-quota.png)

2. 上**创建配额**部分，键入配额的名称、 设置配额，所需的参数，然后单击**确定**。

   ![配额名称](media/asdk-offer-services/quota-properties.png)

3. 选择为你创建的配额**Microsoft.Compute**服务。

   ![选择配额](media/asdk-offer-services/set-quota.png)

4. 重复步骤 1-3 为网络和存储服务设置配额，然后单击确定上的配额部分。 接下来，单击**确定**上**新计划**部分以完成计划设置。 

   ![所有配额都设置](media/asdk-offer-services/all-quotas-set.png)

5. 完成创建产品/服务通过单击**创建**上的计划部分。 已成功创建提议和将被列为可用的产品/服务时，你将看到一条通知。

   ![提供创建](media/asdk-offer-services/offer-complete.png)

## <a name="set-offer-to-public"></a>为公共的组产品/服务
必须将公共类，以便用户若要选择订阅的提议时查看这些产品/服务。 

产品/服务可以是：
- **公共**： 对所有用户可见。
- **专用**：仅对云管理员可见。 有用时起草计划或产品/服务，或如果云管理员想要创建每个订阅，用户。
- **已解除授权**：已对新订阅方关闭。 云管理员可以使用已解除授权来防止将来订阅，但不影响当前订阅方。

> [!TIP]
> 不到产品/服务的更改对用户立即可见。 若要查看所做的更改，用户可能必须注销并再次登录到[用户门户](https://portal.local.azurestack.external)以查看新的产品/服务。

若要设置为公共的新产品： 

1. 在仪表板菜单中单击“产品”，然后单击创建的产品。

2. 依次单击“更改状态”、“公共”。

   ![公共状态](media/asdk-offer-services/set-public.png)

3. 产品/服务将可在 Azure 堆栈用户门户中。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 创建产品
> * 创建计划
> * 设置配额
> * 为公共的组产品/服务

前进到下一步的教程，若要了解  璹綷你刚刚创建以 Azure 堆栈的用户的提议。

> [!div class="nextstepaction"]
> [订阅产品/服务](asdk-subscribe-services.md)