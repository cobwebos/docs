---
title: "在 Azure 堆栈中创建提议 |Microsoft 文档"
description: "作为云管理员，了解如何在 Azure 堆栈中创建用于你的用户的提议。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 96b080a4-a9a5-407c-ba54-111de2413d59
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/10/2017
ms.author: erikje
ms.openlocfilehash: 269a6106f657536ba74be366f842b2f9cd86c5dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-offer-in-azure-stack"></a>在 Azure 堆栈中创建提议

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

[提供](azure-stack-key-features.md)是组的一个或多个提供程序显示给用户购买或订阅的计划。 本文档演示如何创建包括提议[你创建的计划](azure-stack-create-plan.md)在最后一步。 此优惠使订阅服务器能够设置虚拟机。

1. 登录到 Azure 堆栈管理员门户 (https://adminportal.local.azurestack.external) > 单击**新建** > **租户提供 + 计划** >  **提供**。

   ![](media/azure-stack-create-offer/image01.png)
2. 在**新提供**边栏选项卡，填写**显示名称**和**资源名称**，然后选择一个新的或现有**资源组**。 显示名称是产品/服务的友好名称，是有关用户将看到的订阅的提议的唯一信息。 因此，请务必使用直观名称，可帮助用户了解新增功能附带的产品/服务。 只有管理员可以查看资源名称。 它是管理员使用，以便为 Azure 资源管理器资源提供的名称。

   ![](media/azure-stack-create-offer/image01a.png)
3. 单击**基本计划**并在**计划**边栏选项卡，选择你想要包含在产品/服务，然后单击的计划**选择**。 单击**创建**以创建提议。

   ![](media/azure-stack-create-offer/image02.png)
4. 单击**的所有资源**，搜索你新提议，单击新的产品/服务，单击**更改状态**，然后单击**公共**。

   ![](media/azure-stack-create-offer/image03.png)

必须将公共类，以便用户能够获得的完整视图，当订阅时提供。 提供可以是：

* **公共**： 对用户可见。
* **私有**： 仅对云管理员可见。 有用时起草计划或产品/服务，或如果云管理员想要批准每个订阅。
* **解除授权**： 关闭了与新的订阅服务器。 云管理员可以使用已解除授权，以防止将来订阅，但保留当前订户不变。

不到产品/服务的更改对用户立即可见。 若要查看所做的更改，你可能需要在注销/登录以查看在"订阅选取器"中的新订阅时创建资源/资源组。

> [!NOTE]
>你还可以使用 PowerShell 中所述创建默认提供程序、 计划和配额[Azure 堆栈服务管理员自述](https://github.com/Azure/AzureStack-Tools/tree/master/ServiceAdmin)。
>


### <a name="next-steps"></a>后续步骤
[订阅产品，然后设置虚拟机](azure-stack-subscribe-plan-provision-vm.md)
