---
title: 本教程介绍如何创建 Azure Stack 产品/服务 | Microsoft Docs
description: 了解 如何创建包括计划和配额在内的 Azure Stack 产品/服务。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/27/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 8bcc2f3077e79ff83ac2e90db0bb0fa53ae83adc
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="tutorial-offer-azure-stack-iaas-services"></a>教程：提供 Azure Stack IaaS 服务
Azure Stack 云管理员可以创建产品/服务供用户（有时称为租户）订阅。 然后，用户可以通过其订阅来使用 Azure Stack 服务。

本教程介绍如何创建一个产品/服务，以便用户创建基于 Azure Stack Marketplace Windows Server 2016 Datacenter 映像（已在[上一教程](asdk-marketplace-item.md)中上传）的虚拟机。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建产品
> * 创建计划
> * 设置配额
> * 将产品/服务设置为公共

在 Azure Stack 中，可通过订阅、产品/服务和计划将服务交付给用户。 用户可以订阅多个产品/服务。 产品/服务可以包含一个或多个计划，计划可以包含一个或多个服务，如下图所示：

![订阅、产品/服务和计划](media/asdk-offer-services/sop.png)

统会依次提示作为 Azure Stack 操作员来提供服务的你创建产品/服务、计划和配额。 创建产品/服务以后，Azure Stack 用户即可通过用户门户订阅该产品/服务。

## <a name="create-an-offer"></a>创建产品
**产品/服务**是 Azure Stack 操作员提供给用户购买或订阅的一个或多个计划的组合。

1. 以云管理员身份登录到 [Azure Stack 门户](https://adminportal.local.azurestack.external)。

2. 单击“新建” > “产品/服务 + 计划” > “产品/服务”。

   ![新产品/服务](media/asdk-offer-services/new-offer.png)

2. 在“新建产品/服务”部分填写“显示名称”和“资源名称”，然后选择一个新的或现有的**资源组**。 显示名称是用户可看到的产品/服务的公开友好名称。 只有云操作员可以看到资源名称，管理员可以将该名称用于充当 Azure 资源管理器资源的产品/服务。

   ![显示名称](media/asdk-offer-services/offer-display-name.png)


## <a name="create-a-plan"></a>创建计划
在输入基本的产品/服务信息以后，必须向该产品/服务添加至少一个基本计划。 

Azure Stack 操作员可以通过**计划**将服务与关联的配额组合到一起提供给用户。

1. 单击“基本计划”，在“计划”部分单击“添加”，将新计划添加到产品/服务。

   ![添加计划](media/asdk-offer-services/new-plan.png)

2. 在“新建计划”部分填写“显示名称”和“资源名称”。 显示名称是用户可以看到的计划的友好名称。 只有云操作员可以看到资源名称，云操作员可以将该名称用于充当 Azure 资源管理器资源的计划。

   ![计划显示名称](media/asdk-offer-services/plan-display-name.png)

3. 接下来，请选择要包括在计划中的服务。 若要提供 IaaS 服务，请单击“服务”，选择 **Microsoft.Compute**、**Microsoft.Network** 和 **Microsoft.Storage**，然后单击“选择”。

   ![计划服务](media/asdk-offer-services/select-services.png)


## <a name="set-quotas"></a>设置配额
由于产品/服务的计划包含服务，因此必须为每个服务设置配额。 

就提供的计划中包含的每个服务来说，**配额**决定了可供用户使用的资源量。

1. 单击“配额”，然后选择要为其创建配额的服务。 

   开始时，请先为计算服务创建配额。 在命名空间列表中，选择“Microsoft.Compute”命名空间，然后单击“创建新配额”。
   
   ![创建新配额](media/asdk-offer-services/create-quota.png)

2. 在“创建配额”部分，输入配额名称并为配额设置所需的参数，然后单击“确定”。

   ![配额名称](media/asdk-offer-services/quota-properties.png)

3. 选择为 **Microsoft.Compute** 服务创建的配额。

   ![选择配额](media/asdk-offer-services/set-quota.png)

4. 重复执行设置网络和存储服务配额所需的步骤 1-3，然后单击“配额”部分的“确定”。 接下来单击“新建计划”部分的“确定”，完成计划设置。 

   ![所有设置的配额](media/asdk-offer-services/all-quotas-set.png)

5. 单击“计划”部分的“创建”，完成产品/服务的创建。 成功创建产品/服务以后，会看到一个通知。该产品/服务会作为可用产品/服务列出。

   ![创建的产品/服务](media/asdk-offer-services/offer-complete.png)

## <a name="set-offer-to-public"></a>将产品/服务设置为公共
必须将产品/服务设为公共，然后用户才能在选择要订阅的产品/服务时看到它们。 

产品/服务可以是：
- **公共**：对所有用户可见。
- **专用**：仅对云管理员可见。 在以下情况下有用：起草计划或产品/服务时，或者云管理员想要为用户创建每个订阅时。
- **已解除授权**：已对新订阅方关闭。 云管理员可以使用已解除授权来防止将来订阅，但不影响当前订阅方。

> [!TIP]
> 产品/服务的更改不会对用户立即可见。 若要查看所做的更改，用户可能需要先注销，然后重新登录到[用户门户](https://portal.local.azurestack.external)，这样才能看到新的产品/服务。

若要将新的产品/服务设置为公共，请执行以下操作： 
   - 1803 及更高版本： 
     1. 在仪表板菜单中单击“产品/服务”，然后单击创建的产品/服务。

     2. 单击**可访问性状态**，然后单击**公共**。

        ![更改状态](media/asdk-offer-services/change-state.png)

     3. 现在，该产品/服务会出现在 Azure Stack 用户门户中。


   - 之前的版本 1803年:  
     1. 在仪表板菜单中单击“产品/服务”，然后单击创建的产品/服务。

     2. 依次单击“更改状态”、“公共”。

        ![公共状态](media/asdk-offer-services/set-public.png)

     3. 现在，该产品/服务会出现在 Azure Stack 用户门户中。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 创建产品
> * 创建计划
> * 设置配额
> * 将产品/服务设置为公共

前进到下一步的教程，若要了解  璹綷你刚刚创建以 Azure 堆栈的用户的提议。

> [!div class="nextstepaction"]
> [订阅产品/服务](asdk-subscribe-services.md)