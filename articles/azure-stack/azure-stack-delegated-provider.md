---
title: "委派 Azure 堆栈的产品/服务 |Microsoft 文档"
description: "了解如何将负责创建提议和注册用户的其他人。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 157f0207-bddc-42e5-8351-197ec23f9d46
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: brenduns
ms.reviewer: alfredop
ms.openlocfilehash: 06690d5251954b204b28928b3fe670669000aa7c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="delegate-offers-in-azure-stack"></a>在 Azure Stack 中委托产品/服务

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

作为 Azure 堆栈操作员中，你通常想要将负责创建提议和注册用户的其他人。 例如，如果你是服务提供商，你可能想客户注册和管理它们代表你的经销商。 或者，如果你在企业中的中心 IT 小组的一部分，你可能想子公司注册用户无需你干预。

通过使访问和管理更多用户不是你可以直接中，委派帮助您对这些任务。 下图显示了一个级别的委派，但 Azure 堆栈支持多个级别。 委派的提供程序 (Dp) 可以反过来委托给其他提供程序，最多五个级别。

![委派的级别](media/azure-stack-delegated-provider/image1.png)

Azure 堆栈运算符可以通过使用的委派功能委托给其他用户的用户和提供的创建。

## <a name="roles-and-steps-in-delegation"></a>角色和中的委派的步骤
若要了解委派，请记住，有三个角色涉及：

* *Azure 堆栈运算符*管理 Azure 堆栈基础结构，则创建的产品/服务模板，并且委托其他人提供给其用户。

* 委派的 Azure 堆栈运算符被称作*委派提供程序*。 它们可以属于其他组织，如其他 Azure Active Directory (Azure AD) 用户。

* *用户*注册的提供和使用它们来管理其工作负荷，创建 Vm，存储数据，等等。

下图中所示，有两个步骤来设置委派。

1. *标识委派的提供程序*。 通过订阅到提议基于包含仅订阅服务的计划执行此操作。 订阅此产品的用户获取的某些 Azure 堆栈运算符的功能，包括的功能来扩展提供以及登录用户注册它们。

2. *委托给委派的提供程序的提议*。 此优惠函数作为的委派提供程序可以提供的模板。 委派的提供程序现在可以拍摄提议。 为它选择的名称 （但不更改其服务和配额），并向用户提供。

![创建委派的提供程序，并使其能够注册用户](media/azure-stack-delegated-provider/image2.png)

若要充当委派的提供程序，用户需要与主要提供程序建立关系。 换而言之，他们需要创建订阅。 在此方案中，此订阅中标识为具有该权限授予代表的主要提供商存在提供的委派提供程序。

建立这种关系后，Azure 堆栈运算符可以委托给委派的提供程序的提议。 委派的提供程序现在能以需要产品/服务、 将其重命名 （但不是更改其实质），并向其客户提供。

下列各节介绍如何建立委派的提供程序、 委托优惠后，并验证用户可以注册它。

## <a name="set-up-roles"></a>设置角色

若要查看在工作的委派提供程序，你需要其他 Azure AD 帐户，除了你的 Azure 堆栈运算符帐户。 如果你没有它们，创建两个帐户。 帐户可以属于任何 Azure AD 用户。 我们将它们用作委派的提供程序和用户。

| **角色** | **组织的权限** |
| --- | --- |
| 委派的提供程序 |用户 |
| 用户 |用户 |

## <a name="identify-the-delegated-providers"></a>标识委派的提供程序
1. Azure 堆栈运算符以登录。

2. 创建提议，使用户能够成为委派的提供程序：
   
   a.  [创建计划](azure-stack-create-plan.md)。
       此计划应包括仅订阅服务。 在本文中，我们将使用计划调用**PlanForDelegation**。
   
   b.  [创建提议](azure-stack-create-offer.md)基于该计划。 在本文中，我们将使用调用提议**OfferToDP**。
   
   c.  提议的创建操作完成后，添加到此产品/服务作为订阅服务器的委派的提供程序。 执行此操作通过选择**订阅** > **添加** > **新租户订阅**。
   
   ![添加委派的提供程序作为订阅服务器](media/azure-stack-delegated-provider/image3.png)

> [!NOTE]
> 你拥有所有 Azure 堆栈提议，使公共和从而用户的产品/服务的选项注册它，或使其保持私有和使 Azure 堆栈操作员能够管理的注册。 委派的提供程序通常是小的一组。 你想要他将获准给它，以便保持此优惠私有意义在大多数情况下的控制。
> 
> 

## <a name="azure-stack-operator-creates-the-delegated-offer"></a>Azure 堆栈运算符创建委派的提议

你现在已经建立了你的委派提供程序。 下一步是创建计划和想要委托，，和你的客户将使用哪些产品/服务。 它是完全按照你希望看到它，因为委派的提供程序将无法更改计划和配额，它包含客户定义此产品/服务的一个好办法。

1. 作为 Azure 堆栈操作员，[创建计划](azure-stack-create-plan.md)和[提议](azure-stack-create-offer.md)其为基础。 对于本文中，我们使用称为提议**DelegatedOffer。**
   
   > [!NOTE]
   > 此优惠不必是公共的。 如果选择，你可以将其公开。 在大多数情况下，但是，你仅希望委派提供程序能够对其进行访问。 以下步骤中所述委派私有优惠后，委派的提供程序将有权访问它。
   > 
   > 
1. 委托提议。 转到**DelegatedOffer。** 然后，在**设置**窗格中，选择**委派提供程序** > **添加**。

2. 从下拉列表框中，选择委派的提供程序的订阅，然后选择**委托**。

> ![添加委派提供程序](media/azure-stack-delegated-provider/image4.png)
> 
> 

## <a name="delegated-provider-customizes-the-offer"></a>委派的提供程序自定义产品/服务

作为委派的提供程序登录到用户门户。 通过使用委派的提议作为模板，然后创建新提议。

1. 选择**新** > **租户提供 + 计划** > **提供**。

    ![创建新提议](media/azure-stack-delegated-provider/image5.png)


1. 为服务分配一个名称。 在这里，我们选择**ResellerOffer**。 选择要基于它，然后选择所依据的委派的产品**创建**。
   
   ![指定一个名称](media/azure-stack-delegated-provider/image6.png)

    >[!NOTE] 
    > 请注意比较，以提供为遇到的 Azure 堆栈运算符创建的差异。 委派的提供程序并不构造从基本计划和外接程序计划产品/服务。 他们仅可以选择从已委派给它们，并不能对这些产品/服务进行更改的产品/服务。

1. 通过选择公开优惠**浏览**，，然后**提供**。 选择的产品，然后选择**更改状态**。

2. 委派的提供程序公开这些服务通过自己门户 URL。 这些服务提供商只能通过委派门户可见。 若要查找和更改此 URL:
   
    a.  选择**浏览** > **更多的服务** >  **订阅**。 然后选择的委派提供程序订阅。 在本例中，它具有**DPSubscription** > **属性**。
   
    b.  门户将复制到单独的位置，如记事本的 URL。
   
    ![选择的委派提供程序订阅](media/azure-stack-delegated-provider/dpportaluri.png)  
   
   现在已为委派的提供程序创建委派的产品/服务。 以委派的提供程序注销。 关闭您一直使用的浏览器窗口。

## <a name="sign-up-for-the-offer"></a>注册产品/服务
1. 在新浏览器窗口中，转到委派门户上一步中保存的 URL。 以用户身份登录到门户。 
   
   >[!NOTE]
   > 使用委派的门户执行此步骤。 否则不可见的委派的产品/服务。

2. 在仪表板中，选择**获取订阅**。 你会看到仅的委派产品/服务创建的委派提供程序会呈现给用户：

> ![查看和选择产品/服务](media/azure-stack-delegated-provider/image8.png)
> 
> 

产品/服务委派过程已完成。 用户可以立即注册此优惠通过为其获取订阅。

## <a name="multiple-tier-delegation"></a>多层委派

多层委派使委派的提供程序，可以将委托提供给其他实体。 这样，例如，更深入的分销商渠道，在其中的提供程序正在管理 Azure 堆栈委托提供给分发服务器的创建。 该分发服务器，反过来委托给分销商。 Azure 堆栈支持最多五个级别的委派。

若要创建多个层的优惠委派，委派的提供程序会将优惠反过来委托给下一个提供程序。 过程是相同的委派提供程序按照原样 Azure 堆栈运算符 (请参阅[Azure 堆栈运算符创建委派的优惠](#cloud-operator-creates-the-delegated-offer))。

## <a name="next-steps"></a>后续步骤
[设置虚拟机](azure-stack-provision-vm.md)

