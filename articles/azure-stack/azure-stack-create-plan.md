---
title: 在 Azure Stack 中创建计划 | Microsoft Docs
description: 作为云管理员，创建一个让订阅方预配虚拟机的计划。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 1fa01d23108ce92fbd7c854442c0474b19395d25
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248697"
---
# <a name="create-a-plan-in-azure-stack"></a>在 Azure Stack 中创建计划

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

[计划](azure-stack-key-features.md)是对一个或多个服务的分组。 作为提供商，可以创建提供给用户的计划。 反过来，用户可以订阅产品/服务，以便使用其所包括的计划和服务。 此示例演示如何创建一个包括计算、网络和存储资源提供程序的计划。 订阅方使用此计划可以预配虚拟机。

1. 登录 Azure Stack 管理员门户 (https://adminportal.local.azurestack.external)。

2. 若要创建用户可以订阅的计划和套餐，请单击“新建” > “套餐 + 计划” > “计划”。
  
   ![选择某一计划](media/azure-stack-create-plan/select-plan.png)

3. 下**新计划**，输入**显示名称**和**资源名称**。 显示名称是用户可以看到的计划的友好名称。 只有管理员可以看到哪些管理员用于为 Azure 资源管理器资源计划使用的资源名称。

   ![指定详细信息](media/azure-stack-create-plan/plan-name.png)

4. 创建一个新**资源组**或选择一个现有资源组，作为计划的容器。

   ![指定资源组](media/azure-stack-create-plan/resource-group.png)

5. 选择“服务”，然后选择与“Microsoft.Compute”、“Microsoft.Network”和“Microsoft.Storage”对应的复选框。 接下来，选择“选择”以保存配置。 将鼠标指针悬停在每个选项上时会显示复选框。
  
   ![选择服务](media/azure-stack-create-plan/services.png)

6. 选择**配额**， **Microsoft.Storage （本地）**，然后选择的默认配额或选择**创建新配额**创建自定义的配额。
  
   ![配额](media/azure-stack-create-plan/quotas.png)

7. 如果要新建配额，请输入配额的**名称** > 指定配额值 > 选择“确定”。 **创建配额**对话框关闭。

   ![新建配额](media/azure-stack-create-plan/new-quota.png)

   然后选择创建的新配额。 选择配额将其分配，并关闭选择对话框。
  
   ![分配配额](media/azure-stack-create-plan/assign-quota.png)

8. 重复步骤 6 和 7 来为“Microsoft.Network (本地)”和“Microsoft.Compute (本地)”创建并分配配额。 当所有三个服务都有分配的配额时，它们将类似下一个示例。

   ![完整的配额分配](media/azure-stack-create-plan/all-quotas-assigned.png)

9. 下**配额**，选择**确定**，然后在**新计划**，选择**创建**以创建计划。

    ![创建计划](media/azure-stack-create-plan/create.png)

10. 若要查看新计划，请选择“所有资源”，然后搜索该计划并选择其名称。 如果资源列表很长，可使用“搜索”来通过名称定位你的计划。

   ![查看计划](media/azure-stack-create-plan/plan-overview.png)

## <a name="next-steps"></a>后续步骤

[创建产品/服务](azure-stack-create-offer.md)
