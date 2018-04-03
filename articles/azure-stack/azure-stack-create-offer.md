---
title: 在 Azure Stack 中创建产品/服务 | Microsoft Docs
description: 作为云管理员，了解如何在 Azure Stack 中为用户创建产品/服务。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 96b080a4-a9a5-407c-ba54-111de2413d59
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/27/2018
ms.author: brenduns
ms.openlocfilehash: 6a59d0c8144492ef907e5c395f05e4e8a16678a5
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="create-an-offer-in-azure-stack"></a>在 Azure Stack 中创建产品/服务

[产品/服务](azure-stack-key-features.md)是提供者提供给用户购买或订阅的一个或多个计划的组合。 本文档演示如何创建包含在上一步中[创建的计划](azure-stack-create-plan.md)的产品/服务。 订阅方使用此产品/服务可以预配虚拟机。

1. 登录到 Azure 堆栈管理员门户 (https://adminportal.local.azurestack.external) > 单击**新建** > **租户提供 + 计划** > **提供**。

   ![](media/azure-stack-create-offer/image01.png)
2. 在“新建产品/服务”窗格中，填写**显示名称**和**资源名称**，然后选择一个新的或现有**资源组**。 显示名称是产品/服务的友好名称。 此友好名称是用户在订阅时可看到的有关该产品/服务的唯一信息。 因此，请务必使用直观名称，以帮助用户了解该产品/服务附带了什么功能。 只有管理员可以看到资源名称。 管理员使用此名称将该产品/服务作为 Azure 资源管理器资源处理。

   ![](media/azure-stack-create-offer/image01a.png)
3. 单击“基本计划”以打开“计划”窗格，选择要包含该产品/服务的计划，然后单击“选择”。 单击“创建”以创建该产品/服务。

   ![](media/azure-stack-create-offer/image02.png)
4. 在创建后的产品/服务，你可以更改其状态。 必须将提供*公共*用户它们在订阅时获得的完整视图。 产品/服务可以是：
   - **公共**：对用户可见。
   - **私有**： 仅可见云管理员。 在以下情况下有用：起草计划或产品/服务时，或者如果云管理员想要[为用户创建每个订阅](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator)。
   - **已解除授权**：已对新订阅方关闭。 云管理员可以使用已解除授权来防止将来订阅，但不影响当前订阅方。

   > [!TIP]  
   > 产品/服务的更改不会对用户立即可见。 若要查看所做的更改，用户可能必须注销并再次登录用户门户，以便查看新的产品/服务。 

   若要更改产品/服务的状态： 

   - **1803 及更高版本**:  
     在提议概述边栏选项卡，单击**可访问性状态**，选择你想要使用，如的状态*公共*，然后单击**保存**。 
 
     ![选择可访问性状态](media/azure-stack-create-offer/change-state.png) 

     或者，你访问某项服务后你可以转到**提供设置**，然后选择**可访问性状态**的状态更改。 

   - **之前的版本 1803年**:  
     单击“所有资源”，搜索新产品/服务，单击该新产品/服务，单击“更改状态”，然后单击“公共”。

  
   > [!NOTE] 
   > 你还可以使用 PowerShell 创建默认提供程序、 计划和配额。 有关详细信息，请参阅[Azure 堆栈服务管理员自述](https://github.com/Azure/AzureStack-Tools/tree/master/ServiceAdmin)。
   >


### <a name="next-steps"></a>后续步骤
[创建订阅](azure-stack-subscribe-plan-provision-vm.md)      
[预配虚拟机](azure-stack-provision-vm.md)
