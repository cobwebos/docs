---
title: 在 Azure Stack 中创建套餐 | Microsoft Docs
description: 作为云管理员，了解如何在 Azure Stack 中为用户创建套餐。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 230415b84f06beac549693d49055d7cb998163d8
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251446"
---
# <a name="create-an-offer-in-azure-stack"></a>在 Azure Stack 中创建套餐

[提供了](azure-stack-key-features.md)是提供程序提供给用户，这些用户可以购买或订阅的一个或多个计划的组。 本文介绍如何创建产品/服务，包括[创建的计划](azure-stack-create-plan.md)。 此产品/服务使订户能够设置虚拟机 (Vm)。

1. 登录到[Azure Stack 管理员门户](https://adminportal.local.azurestack.external)，然后选择 **+ 创建资源**，然后**租户产品/服务 + 计划**，然后**提供**。

   ![创建产品](media/azure-stack-create-offer/image01.png)
  
2. 在“新建套餐”下，输入“显示名称”和“资源名称”，然后在“资源组”下选择“新建”或“使用现有项”。 显示名称是产品/服务的友好名称。 此友好名称，是关于用户在他们所订阅产品/服务时看到的产品/服务的唯一信息。 请使用直观名称，以帮助用户了解该套餐附带了什么功能。 只有管理员可以看到资源名称。 管理员使用此名称将该产品/服务作为 Azure 资源管理器资源处理。

   ![新建产品/服务](media/azure-stack-create-offer/image01a.png)
  
3. 选择“基本计划”打开“计划”。 选择要包含在套餐中的计划，然后选择“选择”。 若要创建套餐，请选择“创建”。

   ![选择计划](media/azure-stack-create-offer/image02.png)
  
4. 创建套餐后，可以更改其状态。 产品/服务必须设**公共**供用户在订阅时获得完整视图。 产品/服务可以是：

   - **公共**:对用户可见。
   - **专用**：仅对云管理员可见。 起草计划或套餐时，或者当云管理员想要[为用户创建每个订阅](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator)时，此设置非常有用。
   - **已解除授权**:关闭到新的订阅服务器。 云管理员可以解除授权的产品/服务以防止将来订阅，但不影响当前订阅不受影响。

   > [!TIP]  
   > 产品/服务的更改不会对用户立即可见。 若要查看所做的更改，用户可能需要先注销，然后重新登录到用户门户，这样才能看到新的套餐。

   在产品/服务的概述屏幕中，选择**辅助功能状态**。 选择你想要使用的状态 (例如，**公共**)，然后选择**保存**。

     ![选择状态](media/azure-stack-create-offer/change-stage-1807.png)

     或者，选择“更改状态”，然后选择状态。

    ![选择辅助功能状态](media/azure-stack-create-offer/change-stage-select-1807.png)

   > [!NOTE]
   > 还可以使用 PowerShell 来创建默认套餐、计划和配额。 有关详细信息，请参阅 [Azure Stack PowerShell 模块 1.4.0](/powershell/azure/azure-stack/overview?view=azurestackps-1.4.0)。

## <a name="next-steps"></a>后续步骤

- [创建订阅](azure-stack-subscribe-plan-provision-vm.md)
- [预配虚拟机](azure-stack-provision-vm.md)
