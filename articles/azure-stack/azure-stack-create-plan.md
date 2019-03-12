---
title: 在 Azure Stack 中创建计划 | Microsoft Docs
description: 作为云管理员，创建一个让订阅方预配虚拟机的计划。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: a2ac138228b7b54f486acb0f42975748136a8da7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2019
ms.locfileid: "57759463"
---
# <a name="create-a-plan-in-azure-stack"></a>在 Azure Stack 中创建计划

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

[计划](azure-stack-key-features.md)是分组的一个或多个服务和其配额。 作为提供商，可以创建提供给用户的计划。 反过来，用户可以订阅产品/服务，用于计划、 服务和它们包含的配额。 此示例演示如何创建一个包括计算、网络和存储资源提供程序的计划。 订阅方使用此计划可以预配虚拟机。

## <a name="create-a-plan-1902-and-later"></a>创建一个计划 （1902年及更高版本）

1. 登录到 [Azure Stack 管理员门户](https://adminportal.local.azurestack.external)。

2. 若要创建用户可以订阅的计划和套餐，请依次选择“创建资源”、“套餐 + 计划”、“计划”。
  
   ![选择计划](media/azure-stack-create-plan/select-plan.png)

3. 将显示的选项卡式的用户界面，可指定计划名称、 添加服务，并为每个所选的服务定义配额。 最重要的是，你可以查看创建之前你决定创建它的产品/服务的详细信息。

   下**基础知识**选项卡**新计划**窗口中，输入**显示名称**和一个**资源名称**。 显示名称是操作员可以看到的计划的友好名称。 请注意，在管理员门户中，计划的详细信息仅对运算符可见。

   ![指定详细信息](media/azure-stack-create-plan/plan-name.png)

4. 创建一个新**资源组**或选择一个现有资源组，作为计划的容器。

   ![指定资源组](media/azure-stack-create-plan/resource-group.png)

5. 选择**Services**卡，并选择复选框**Microsoft.Compute**， **Microsoft.Network**，以及**Microsoft.Storage**.
  
   ![选择服务](media/azure-stack-create-plan/services.png)

6. 选择**配额**选项卡。下一步**Microsoft.Storage**中，从下拉列表框中，选择默认配额或选择**创建新**来创建自定义的配额。
  
   ![配额](media/azure-stack-create-plan/quotas.png)

7. 如果你要创建新的配额，请输入**名称**配额，然后指定的配额值。 选择**确定**创建配额。

   ![新建配额](media/azure-stack-create-plan/new-quota.png)

8. 重复步骤 6 和 7 来创建和分配的配额**Microsoft.Network**并**Microsoft.Compute**。 为所有三个服务分配配额后，这些服务将如以下示例所示。

   ![完成配额分配](media/azure-stack-create-plan/all-quotas-assigned.png)

9. 选择**查看 + 创建**若要查看计划。 查看所有值和配额，以确保它们正确无误。 请注意每个服务/配额对左侧的展开箭头。 一项新功能，可在所选的计划中，一次，在计划中查看每个配额的详细信息，并返回去进行任何必要的编辑一个地展开配额。

   ![创建计划](media/azure-stack-create-plan/create.png)

10. 准备就绪后，选择**创建**以创建计划。

11. 若要查看新计划，请选择**计划**，然后搜索该计划并选择其名称。 如果资源列表很长，可使用“搜索”来通过名称定位你的计划。

## <a name="create-a-plan-1901-and-earlier"></a>创建一个计划 （1901年及更早版本）

1. 登录到 [Azure Stack 管理员门户](https://adminportal.local.azurestack.external)。

2. 若要创建用户可以订阅的计划和套餐，请依次选择“创建资源”、“套餐 + 计划”、“计划”。
  
   ![选择计划](media/azure-stack-create-plan/select-plan1901.png)

3. 在“新建计划”下，输入**显示名称**和**资源名称**。 显示名称是计划的易记名称，用户可以看到它。 只有管理员可以看到资源名称，管理员使用此名称将计划作为 Azure 资源管理器资源进行处理。

   ![指定详细信息](media/azure-stack-create-plan/plan-name1901.png)

4. 创建一个新**资源组**或选择一个现有资源组，作为计划的容器。

   ![指定资源组](media/azure-stack-create-plan/resource-group1901.png)

5. 选择“服务”，然后选择与“Microsoft.Compute”、“Microsoft.Network”和“Microsoft.Storage”对应的复选框。 接下来，选择“选择”以保存配置。 将鼠标指针悬停在每个选项上时会显示复选框。
  
   ![选择服务](media/azure-stack-create-plan/services1901.png)

6. 选择“配额”、“Microsoft.Storage (本地)”，然后选择默认配额，或选择“新建配额”来创建自定义的配额。
  
   ![配额](media/azure-stack-create-plan/quotas1901.png)

7. 如果要新建配额，请输入配额的**名称** > 指定配额值 > 选择“确定”。 “创建配额”对话框关闭。

   ![新建配额](media/azure-stack-create-plan/new-quota1901.png)

   然后选择创建的新配额。 选择配额会分配它并关闭选择对话框。
  
   ![分配配额](media/azure-stack-create-plan/assign-quota1901.png)

8. 重复步骤 6 和 7 来为“Microsoft.Network (本地)”和“Microsoft.Compute (本地)”创建并分配配额。 为所有三个服务分配配额后，这些服务将如以下示例所示。

   ![完成配额分配](media/azure-stack-create-plan/all-quotas-assigned1901.png)

9. 在“配额”下选择“确定”，然后在“新建计划”下选择“创建”以创建计划。

    ![创建计划](media/azure-stack-create-plan/create1901.png)

10. 若要查看新计划，请选择“所有资源”，然后搜索该计划并选择其名称。 如果资源列表很长，可使用“搜索”来通过名称定位你的计划。

    ![检查计划](media/azure-stack-create-plan/plan-overview1901.png)

## <a name="next-steps"></a>后续步骤

* [创建产品/服务](azure-stack-create-offer.md)
