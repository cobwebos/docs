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
ms.date: 06/07/2018
ms.author: brenduns
ms.openlocfilehash: e5b96a9464bf4d0e3b69d2f635da32c6648ce793
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247511"
---
# <a name="create-an-offer-in-azure-stack"></a>在 Azure Stack 中创建产品/服务

[提供](azure-stack-key-features.md)是组的一个或多个提供程序向用户购买或订阅的计划。 本文档演示如何创建包括提议[你创建的计划](azure-stack-create-plan.md)。 此优惠使订阅服务器能够设置虚拟机。

1. 登录到 Azure 堆栈管理员门户 (https://adminportal.local.azurestack.external)和选择**新建** > **租户提供 + 计划** > **提供**。

   ![创建产品](media/azure-stack-create-offer/image01.png)
  
2. 下**新提供**，输入**显示名称**和**资源名称**，然后在**资源组**，选择**创建新**或**使用现有**。 显示名称是产品/服务的友好名称。 此友好名称是有关用户他们所订阅产品/服务时看到的提议的唯一信息。 使用可帮助用户了解新增功能附带优惠直观名称。 只有管理员可以看到资源名称。 管理员使用此名称将该产品/服务作为 Azure 资源管理器资源处理。

   ![新提议](media/azure-stack-create-offer/image01a.png)
  
3. 选择**基本计划**以打开**计划**。 选择你想要包含在产品/服务，然后选择的计划**选择**。 若要创建优惠选择**创建**。

   ![选择计划](media/azure-stack-create-offer/image02.png)
  
4. 创建产品/服务后，可以更改其状态。 必须将产品/服务设为“公共”，用户才能在订阅时获得完整视图。 产品/服务可以是：

   - **公共**：对用户可见。
   - **专用**：仅对云管理员可见。 此设置很有用时起草计划或产品/服务，或如果想要将云管理员联系[创建每个订阅，用户](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator)。
   - **已解除授权**：已对新订阅方关闭。 云管理员可以使用已解除授权，以防止将来订阅，但保留当前的订阅服务器不受影响。

   > [!TIP]  
   > 不会对用户立即可见到产品/服务的更改。 若要查看所做的更改，用户可能需要注销并再次登录到用户门户以查看新的产品/服务。

   若要更改产品/服务的状态：

   - **1803 和更高版本**：  
     提议的概述，在选择**可访问性状态**。 选择你想要使用的状态 (例如，*公共*)，然后选择**保存**。
 
     ![选择辅助功能状态](media/azure-stack-create-offer/change-state.png)

     作为替代方法，你访问某项服务后，可以转到**提供设置**。 选择**可访问性状态**的状态更改。

   - **低于 1803 的版本**：  
     选择**的所有资源**，对于新的产品，搜索，然后选择新提议。 选择**更改状态**，然后选择**公共**。

   > [!NOTE]
   > 还可以使用 PowerShell 来创建默认产品/服务、计划和配额。 有关详细信息，请参阅 [Azure Stack 服务管理员自述文件](https://github.com/Azure/AzureStack-Tools/tree/master/ServiceAdmin)。

## <a name="next-steps"></a>后续步骤

- [创建订阅](azure-stack-subscribe-plan-provision-vm.md)
- [预配虚拟机](azure-stack-provision-vm.md)
