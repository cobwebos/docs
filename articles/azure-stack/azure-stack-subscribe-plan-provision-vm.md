---
title: 在 Azure Stack 中订阅产品/服务 | Microsoft Docs
description: 在 Azure Stack 中创建产品/服务的订阅
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/11/2018
ms.author: brenduns
ms.openlocfilehash: 9153649774a67533649fb62da83a3f50abd592da
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295204"
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>在 Azure Stack 中创建产品/服务的订阅

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

[创建某个产品/服务](azure-stack-create-offer.md)后，用户需要订阅此产品/服务才能使用此产品/服务。 有两种方法，用户可以获取订阅到产品/服务：

- 云操作员可以从管理员门户内部为用户创建订阅。 创建的订阅可用于公共和专用产品/服务。
- 租户用户可以在使用用户门户时订阅公共产品/服务。  

## <a name="create-a-subscription-as-a-cloud-operator"></a>以云操作员的身份创建订阅

云操作员可使用管理员门户为用户创建产品/服务订阅。  你可以为自己的目录租户成员创建订阅。  如果已启用[多租户](azure-stack-enable-multitenancy.md)，则还可以为其他目录租户中的用户创建订阅。

如果不希望你的租户能够创建自己的订阅，将您提供设为私有，，然后创建你的租户订阅。 将 Azure Stack 与外部计帐系统或服务目录系统集成时，通常会使用此方法。

创建用户的订阅后，他们可以登录到用户门户并查看它们已订阅的提议。  

### <a name="to-create-a-subscription-for-a-user"></a>若要为用户创建订阅

1. 在管理门户中，转到“用户订阅”。
2. 选择 **添加** 。 下**新用户订阅**，输入以下信息：  

   - **显示名称**– 标识显示为该订阅的友好名称*用户订阅名称*。
   - **用户**– 指定此订阅可用的目录租户中的用户。 用户名显示为“所有者”。  用户名的格式取决于标识解决方案。 例如：

     - **Azure AD:***&lt;user1 > @&lt;contoso.onmicrosoft.com >* 

     - **AD FS：***&lt;user1>@&lt;azurestack.local>*

   - **目录租户**– 选择其中的用户帐户所属的目录租户。 如果尚未启用多租户，将用你的本地目录租户。

3. 选择“套餐”。 下**提供**，选择**提供**此订阅。 由于你要创建用户的订阅，请选择**私有**为可访问性状态。

4. 选择“创建”以创建订阅。 你将看到新的订阅下**用户订阅**。 当用户登录到用户门户时则可以查看订阅详细信息。

### <a name="to-make-an-add-on-plan-available"></a>提供附加计划

云操作员随时可将附加计划添加到以前创建的订阅：

1. 在管理门户中，选择**更服务** > **用户订阅**。 选择想要更改的订阅。

2. 选择**外接程序**，然后选择 **+ 添加**。  

3. 下**添加计划**，作为外接程序中选择所需的计划。

## <a name="create-a-subscription-as-a-user"></a>以用户的身份创建订阅

作为用户，你可以登录到用户门户，以便查找并订阅到公共产品/服务和外接程序计划为你的目录租户 （组织）。

>[!NOTE]
>如果你的 Azure 堆栈环境支持[多租户](azure-stack-enable-multitenancy.md)还可以从远程目录租户订阅到产品/服务。

### <a name="to-subscribe-to-an-offer"></a>订阅产品/服务

1. [登录](azure-stack-connect-azure-stack.md)到用户门户，Azure 堆栈 (https://portal.local.azurestack.external)和选择**获取订阅**。

   ![获取订阅](media/azure-stack-subscribe-plan-provision-vm/image01.png)
  
2. 下**获取订阅**，输入中的订阅的友好名称**显示名称**。 选择**提供**并在列表视图**选择提议**，选取提议。 选择“创建”以创建订阅。

   ![创建产品](media/azure-stack-subscribe-plan-provision-vm/image02.png)
  
3. 订阅产品/服务之后，请刷新门户以查看哪些服务是新订阅的一部分。
4. 若要查看你创建的订阅，选择**更多的服务**，然后选择**订阅**。 选择要查看订阅详细信息的订阅。  

### <a name="to-subscribe-to-an-add-on-plan"></a>订阅附加计划

如果产品/服务有附加计划，随时可将该计划添加到订阅。  

1. 在用户门户中，选择“更多服务” > “订阅”，然后选择要更改的订阅。 如果有可用的所有外接程序计划， **+ 添加计划**处于活动状态，并且没有为磁贴**外接程序计划**。

   >[!NOTE]
   >如果 **+ 添加计划**不活动状态，则与该订阅关联的提议任何外接程序计划不存在。

1. 选择 **+ 添加计划**或**外接程序计划**磁贴。 下**外接程序计划**，选择你想要添加的计划。

## <a name="next-steps"></a>后续步骤

[预配虚拟机](azure-stack-provision-vm.md)
