---
title: 在 Azure Stack 中委托套餐 | Microsoft Docs
description: 了解如何委托他人来管理创建套餐以及为你注册用户的事情。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2018
ms.author: brenduns
ms.reviewer: alfredop
ms.openlocfilehash: 112586d3ee5f49eab9adb72d41a210e2dd9828d8
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2018
ms.locfileid: "42139443"
---
# <a name="delegate-offers-in-azure-stack"></a>在 Azure Stack 中委托套餐

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

作为 Azure Stack 操作员，你通常需要注册用户并创建订阅委托他人。 例如，服务提供商可能需要经销商来代表他们注册和管理客户。 或者，如果你是企业的中心 IT 小组的成员，则可能需要委托其他 IT 工作人员来注册用户。

委派可以更轻松地访问和管理更多的用户可执行的操作，自行，如下图中所示。 

![委托级别](media/azure-stack-delegated-provider/image1.png)

通过委派，委托的提供商管理的产品/服务 （委托产品/服务），并最终客户从系统管理员处获取不参与该产品/服务下的订阅。 

## <a name="understand-delegation-roles-and-steps"></a>了解委托角色和步骤

### <a name="delegation-roles"></a>委托角色

以下角色是委托的一部分：

* Azure Stack 操作员管理 Azure Stack 基础结构和创建套餐模板。 运算符委托他人将向其租户提供产品/服务。

* 委派的 Azure Stack 操作员所具有的用户*所有者*或*参与者*中订阅的权限调用*委托的提供商*。 他们可能属于其他组织，例如其他 Azure Active Directory (Azure AD) 租户。

* 
  *
  *用户可以注册并使用产品/服务来管理其工作负荷、创建 VM、存储数据，等等。

### <a name="delegation-steps"></a>委托步骤

设置委托的过程包括两个基本步骤：

1. *创建委派的提供程序订阅*通过订阅到产品/服务包含只订阅服务的用户。 订阅此产品/服务的用户可以注册这些产品/服务签名然后向其他用户扩展委托产品/服务。

2. 
  *
  *将套餐委托给受委托的提供商。 此产品/服务，若要创建订阅，或将扩展到其用户的产品/服务委托的提供商。 委托的提供商现在可以接受该套餐，并将其提供给其他用户。

下图显示设置委托的步骤。

![创建委托的提供商，并允许其注册用户](media/azure-stack-delegated-provider/image2.png)

**委托的提供商要求**

若要充当委托的提供商，用户需要通过创建订阅来与主要提供商建立关系。 此订阅标识委托的提供商为具有显示主要提供程序代表委托产品/服务的权限。

建立这种关系后，Azure Stack 操作员就可以将套餐委托给受委托的提供商。 委托的提供商可以接受该套餐，将其重命名（但不更改其实质）并提供给客户。

## <a name="delegation-walkthrough"></a>委托演练

以下部分提供有关设置委托的提供商、委托套餐，以及验证用户是否可以注册委托套餐的实践演练。

### <a name="set-up-roles"></a>设置角色

若要使用此演练，除了 Azure Stack 操作员帐户以外，还需要两个 Azure AD 帐户。 如果没有这两个帐户，需要创建这些帐户。 这些帐户可以属于任何 Azure AD 用户，称为委托的提供商和用户。

| **角色** | **组织权限** |
| --- | --- |
| 委托的提供商 |用户 |
| 用户 |用户 |

### <a name="identify-the-delegated-provider"></a>确定委托的提供商

1. 以 Azure Stack 操作员身份登录到管理员门户。

1. 创建可让用户成为委托提供商的套餐：

   a.  [创建计划](azure-stack-create-plan.md)。
       此计划应该只包括订阅服务。 本文使用名为 **PlanForDelegation** 的计划作为示例。

   b.  根据此计划[创建套餐](azure-stack-create-offer.md)。 本文使用名为 **OfferToDP** 的套餐作为示例。

   c.  选择“订阅” > “添加” > “新建租户订阅”，将委托的提供商作为订阅方添加到此套餐。

   ![将委托的提供商添加为订户](media/azure-stack-delegated-provider/image3.png)

   > [!NOTE]
   > 对于所有 Azure Stack 套餐，可以选择将其公开给用户注册，也可以选择不公开，让 Azure Stack 操作员管理注册。 委托的提供商通常是一个小的组。 需要控制其成员，因此大多数情况下不应公开此产品/服务。

### <a name="azure-stack-operator-creates-the-delegated-offer"></a>Azure Stack 操作员创建委托的套餐

下一步是创建要委托的可供用户使用的计划和套餐。 最好是将此套餐准确地定义成想要用户看到的那种规格，因为委托的提供商不能更改该套餐中包括的计划和配额。

1. 以 Azure Stack 操作员的身份[创建计划](azure-stack-create-plan.md)以及基于该计划的[套餐](azure-stack-create-offer.md)。 本文使用名为 **DelegatedOffer** 的套餐作为示例。

   > [!NOTE]
   > 此套餐不必是公共套餐，但可以根据需要将其设置为公共套餐。 但是，在大多数情况下，你只希望委托的提供商有权访问该套餐。 在你按照以下步骤中的说明委托专用产品/服务以后，委托的提供商即可对其进行访问。

1. 委托套餐。 转到“DelegatedOffer”。 在“设置”下，选择“委托的提供商”**“添加”。** > 

1. 从下拉列表中选择委托的提供商的订阅，然后选择“委托”。

   ![添加委托的提供商](media/azure-stack-delegated-provider/image4.png)

### <a name="delegated-provider-customizes-the-offer"></a>委托的提供商自定义产品/服务

以委托的提供商身份登录到用户门户，然后使用委托的套餐作为模板来创建新的产品/服务。

1. 选择“新建” > “租户套餐 + 计划” > “套餐”。

    ![创建新的产品/服务](media/azure-stack-delegated-provider/image5.png)

1. 为产品/服务指定一个名称。 本文使用 **ResellerOffer** 作为示例。 选择委托的套餐作为模板，然后选择“创建”。

   ![指定名称](media/azure-stack-delegated-provider/image6.png)

   >[!IMPORTANT]
   >请务必了解委托的提供程序可以仅选择的产品/服务委托给他们。 它们不能对这些产品/服务进行更改。 Azure Stack 操作员可以更改这些产品/服务，例如，更改其计划和配额。 委托的提供商不构造从基本计划和外接程序计划的产品/服务。 

3. 委托的提供商可以使这些产品/服务通过自己的公共 URL。 若要使公共产品/服务，请选择**浏览**，然后**提供了**。 选择产品/服务，然后选择“更改状态”。

4. 公共委托产品/服务现仅通过委托的门户可见。 若要查找和更改此 URL，请执行以下操作：

    a.  选择“浏览” > “更多服务” > “订阅”。 然后选择委托的提供程序订阅。 例如“DPSubscription” > “属性”。

    b.  将门户 URL 复制到单独的位置，例如记事本。

    ![选择委托的提供商订阅](media/azure-stack-delegated-provider/dpportaluri.png)  

   现已完成创建委托的套餐作为委托的提供商的过程。 以委托的提供商身份注销，并关闭所用的浏览器窗口。

### <a name="sign-up-for-the-offer"></a>注册产品/服务

1. 在新的浏览器窗口中，转到上一步保存的委托的门户 URL。 以用户身份登录到门户。

   >[!NOTE]
   >除非使用委托的门户，否则委托的套餐不可见。

1. 在仪表板中，选择“获取订阅”。 可以看到，只向用户提供由委托的提供商创建的委托的套餐。

   ![查看和选择产品/服务](media/azure-stack-delegated-provider/image8.png)

委托套餐的过程已完成。 现在，用户可以通过获取套餐订阅来注册此套餐。

## <a name="move-subscriptions-between-delegated-providers"></a>委托的提供商之间移动订阅

如果需要可以属于相同的目录租户的新的或现有委托的提供商订阅之间移动订阅。 这是通过使用 PowerShell cmdlet[移动 AzsSubscription](https://docs.microsoft.com/powershell/module/azs.subscriptions.admin)。

这非常有用：
- 载入的委托的提供商角色并且你将执行的新团队成员要分配到此团队成员用户的订阅之前已在默认提供商订阅中创建。
- 您在同一目录的租户 (Azure Active Directory) 中有多个委托的提供商订阅且需要它们之间移动用户订阅。 这可能是这种情况，其中的团队成员会将团队之间，并且其订阅需要分配到新的团队。


## <a name="next-steps"></a>后续步骤

[预配 VM](azure-stack-provision-vm.md)