---
title: 在 Azure Stack 中委托套餐 | Microsoft Docs
description: 了解如何委托他人来管理创建套餐以及为你注册用户的事情。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: alfredop
ms.openlocfilehash: 1efe64d2057a4dccc0d82a8a99bfbf3eaa719521
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2019
ms.locfileid: "54159105"
---
# <a name="delegate-offers-in-azure-stack"></a>在 Azure Stack 中委托套餐

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure Stack 操作员经常需要委托他人来注册用户和创建订阅。 例如，服务提供商可能需要经销商来代表他们注册和管理客户。 或者，如果您在企业中的中心 IT 小组的一部分，你可能想要委托给其他 IT 员工的用户注册。

委派可以更轻松地访问和管理更多的用户不是可以执行自己下, 图中所示：

![委托级别](media/azure-stack-delegated-provider/image1.png)

借助委托，委托的提供商可以管理套餐（委托的套餐），最终客户可获取该套餐中的订阅，而无需系统管理员介入。

## <a name="understand-delegation-roles-and-steps"></a>了解委托角色和步骤

### <a name="delegation-roles"></a>委托角色

以下角色是委托的一部分：

* Azure Stack 操作员管理 Azure Stack 基础结构和创建套餐模板。 操作员委托他人向其租户提供套餐。

* 委托的 Azure Stack 操作员是名为“委托的提供商”的订阅中拥有“所有者”或“参与者”权限的用户。 他们可能属于其他组织，例如其他 Azure Active Directory (Azure AD) 租户。

* 用户可以注册并使用产品/服务来管理其工作负荷、创建 VM、存储数据，等等。

### <a name="delegation-steps"></a>委托步骤

设置委托的过程包括两个基本步骤：

1. **创建委托的提供商订阅**:订阅产品/服务包含只订阅服务的用户。 然后，订阅此套餐的用户可通过为其他用户注册委托的套餐，将这些套餐扩展到其他用户。

2. **将产品/服务委托给委托的提供商**:此套餐可让委托的提供商创建订阅，或将套餐扩展到其用户。 委托的提供商现在可以接受该套餐，并将其提供给其他用户。

下图显示了设置委派的步骤：

![创建委托的提供商，并允许其注册用户](media/azure-stack-delegated-provider/image2.png)

#### <a name="delegated-provider-requirements"></a>委托的提供商要求

若要充当委托的提供商，用户建立的关系与主提供通过创建订阅。 此订阅表明委托的提供商有权代表主提供商提供委托的套餐。

建立这种关系后，Azure Stack 操作员就可以将套餐委托给受委托的提供商。 委托的提供商可以接受该套餐，将其重命名（但不更改其实质）并提供给客户。

## <a name="delegation-walkthrough"></a>委托演练

以下部分提供有关设置委托的提供商、委托套餐，以及验证用户是否可以注册委托套餐的实践演练。

### <a name="set-up-roles"></a>设置角色

若要使用此演练，除了 Azure Stack 操作员帐户以外，还需要两个 Azure AD 帐户。 如果没有这两个帐户，必须创建它们。 这些帐户可以属于任何 Azure AD 用户，称为委托的提供商和用户。

| **角色** | **组织权限** |
| --- | --- |
| 委托的提供商 |用户 |
| 用户 |用户 |

### <a name="identify-the-delegated-provider"></a>确定委托的提供商

1. 以 Azure Stack 操作员身份登录到管理员门户。

1. 创建可让用户成为委托提供商的套餐：

   a.  [创建计划](azure-stack-create-plan.md)。
       此计划应包括只订阅服务。 本文使用名为 **PlanForDelegation** 的计划作为示例。

   b.  根据此计划[创建套餐](azure-stack-create-offer.md)。 本文使用名为 **OfferToDP** 的套餐作为示例。

   c.  将委托的提供商作为订阅服务器添加到此产品/服务，通过选择**订阅**，然后**添加**，然后**新建租户订阅**。

   ![将委托的提供商添加为订户](media/azure-stack-delegated-provider/image3.png)

   > [!NOTE]
   > 对于所有 Azure Stack 套餐，可以选择将其公开给用户注册，也可以选择不公开，让 Azure Stack 操作员管理注册。 委托的提供商通常是一个小的组。 需要控制其成员，因此大多数情况下不应公开此产品/服务。

### <a name="azure-stack-operator-creates-the-delegated-offer"></a>Azure Stack 操作员创建委托的套餐

下一步是创建要委托的可供用户使用的计划和套餐。 它是完全按照你希望用户看到它，因为委托的提供商不能更改的计划和配额，它包含定义此产品/服务的一个好办法。

1. 以 Azure Stack 操作员的身份[创建计划](azure-stack-create-plan.md)以及基于该计划的[套餐](azure-stack-create-offer.md)。 本文使用名为 **DelegatedOffer** 的套餐作为示例。

   > [!NOTE]
   > 此产品/服务无需是公共的但您可以将其公开。 但是，在大多数情况下，你只希望委托的提供商有权访问该套餐。 在你按照以下步骤中的说明委托专用产品/服务以后，委托的提供商即可对其进行访问。

2. 委托套餐。 转到“DelegatedOffer”。 下**设置**，选择**委托的提供商**，然后选择**添加**。

3. 从下拉列表中，选择委托的提供商的订阅，然后选择**委托**。

   ![添加委托的提供商](media/azure-stack-delegated-provider/image4.png)

### <a name="delegated-provider-customizes-the-offer"></a>委托的提供商自定义产品/服务

以委托的提供商身份登录到用户门户，然后使用委托的套餐作为模板来创建新的产品/服务。

1. 选择 **+ 创建资源**，然后**租户产品/服务 + 计划**，然后选择**提供**。

    ![创建新套餐](media/azure-stack-delegated-provider/image5.png)

2. 为产品/服务指定一个名称。 此示例使用**ResellerOffer**。 选择委托的套餐作为模板，然后选择“创建”。

   ![指定名称](media/azure-stack-delegated-provider/image6.png)

   >[!IMPORTANT]
   >请务必了解委托的提供程序可以仅选择的产品/服务委托给他们。 它们不能对这些产品/服务进行更改。 Azure Stack 操作员可以更改这些产品/服务，例如，更改其计划和配额。 委托的提供商并不会构造从基本计划和外接程序计划的产品/服务。

3. 委托的提供商通过自己的门户 URL 公开这些套餐。 若要公开套餐，请依次选择“浏览”、“套餐”。 选择产品/服务，然后选择“更改状态”。

4. 目前，只能通过委托门户查看公开的已委托套餐。 若要查找和更改此 URL，请执行以下操作：

    a.  选择**浏览**，然后**的所有服务**，然后在**常规**类别中，选择**订阅**。 选择委托的提供商订阅中;例如，**是 dpsubscription**，然后**属性**。

    b.  将门户 URL 复制到单独的位置，例如记事本。

    ![选择委托的提供商订阅](media/azure-stack-delegated-provider/dpportaluri.png)  

   现已完成创建委托的套餐作为委托的提供商的过程。 委托的提供商的身份注销和关闭浏览器窗口。

### <a name="sign-up-for-the-offer"></a>注册产品/服务

1. 在新的浏览器窗口中，转到上一步保存的委托的门户 URL。 以用户身份登录到门户。

   >[!NOTE]
   >除非使用委托的门户，否则委托的套餐不可见。

1. 在仪表板中，选择“获取订阅”。 可以看到，只向用户提供由委托的提供商创建的委托的套餐。

   ![查看和选择产品/服务](media/azure-stack-delegated-provider/image8.png)

委托套餐的过程已完成。 现在用户可以注册此产品/服务通过获取对它的订阅。

## <a name="move-subscriptions-between-delegated-providers"></a>在委托的提供商之间移动订阅

如果需要可以属于相同的目录租户的新的或现有委托的提供商订阅之间移动订阅。 这是通过使用 PowerShell cmdlet[移动 AzsSubscription](/powershell/module/azs.subscriptions.admin)。

此操作在以下情况下十分有用：

* 载入新团队成员将采用委托的提供商角色，然后你想要将分配给此中默认提供商订阅以前创建的团队成员用户订阅。
* 您在相同的目录租户 (Azure Active Directory) 中有多个委托的提供商订阅且需要它们之间移动用户订阅。 此方案可能是一种情况其中的团队成员会将团队之间，并且其订阅需要分配到新的团队。

## <a name="next-steps"></a>后续步骤

* [预配 VM](azure-stack-provision-vm.md)