---
title: "订阅 Azure 堆栈中的产品 |Microsoft 文档"
description: "在 Azure 堆栈中创建服务的订阅"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/06/2018
ms.author: brenduns
ms.openlocfilehash: 9b35b497c264fab2b8352eda82fe6d4e1fc274e9
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>在 Azure 堆栈中创建服务的订阅

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

检查完[创建提议](azure-stack-create-offer.md)，用户需要对该产品的订阅，然后才能使用它。   
- 作为一个云运算符，你可以创建从管理员门户内的用户订阅。  你创建的订阅可能会因公钥和私钥的产品/服务。
- 作为租户用户，你可以为公共服务订阅，当你使用用户门户。  

创建用户和 璹綷为用户提供的订阅时，以下部分提供云操作员的指南。

## <a name="create-a-subscription-as-a-cloud-operator"></a>创建作为云操作员订阅
云操作员可以使用管理门户创建用户的服务关联的订阅。  你可以创建适用于你自己的目录租户的成员的订阅。  当[多租户](azure-stack-enable-multitenancy.md)是启用，你还可以为用户的订阅中创建其他目录租户。

你可以创建公钥和私钥提供的订阅。  如果不希望你的租户能够创建自己的订阅，使所有您提供的专用，，然后创建代表你的租户的订阅。 与外部计费或服务目录系统集成 Azure 堆栈时，此方法很常见。

创建用户订阅后，该用户可以登录到用户门户，然后将查找到的产品/服务订阅。  

### <a name="to-create-the-subscription-for-a-user"></a>若要创建用户的订阅
1.  在管理门户中，转到**用户订阅。**
2.  选择**添加**以打开**新用户订阅**窗格。 在这里你可以指定以下详细信息：  

  a. **显示名称**– 标识显示为该订阅的友好名称*用户订阅名称*。

  b. **用户**– 指定此订阅可用的目录租户中的用户。 用户名称显示为*所有者*。  用户名的格式取决于您的标识解决方案。 例如：   

     -  **Azure AD:**  *&lt;user1>@&lt;contoso.onmicrosoft.com>*

     -   **AD FS:**  *&lt;user1>@&lt;azurestack.local>*     

  c.    **目录租户**– 选择其中的用户帐户所属的目录租户。 如果你尚未启用多租户，将用你的本地目录租户。

3.  选择**提供**以打开**提供**窗格中，然后选择**提供**此订阅。 由于您创建的用户的订阅，因此你可以选择的私有或公有优惠。

4.  选择**创建**创建订阅。 **用户订阅**窗格现在将显示新的订阅。  更高版本，当用户记录到用户门户时，他们可以查看有关此订阅的详细信息。

### <a name="to-make-an-add-on-plan-available"></a>要提供的外接程序计划  
云操作员可以向在任何时间以前创建的订阅添加外接程序计划：   
1.  在管理门户中，转到**更服务** > **用户订阅**，然后选择你想要更改订阅。   

2.  选择**外接程序** > **添加**以打开**添加计划**窗格。  

3.  选择你想要添加为外接程序的计划。  然后，控制台会显示与订阅关联的计划。




## <a name="create-a-subscription-as-a-user"></a>创建作为用户订阅
作为用户，你可以登录到用户门户，以便查找并订阅到公共产品/服务和外接程序计划从你的目录租户 （组织）。 当 Azure 堆栈环境支持[多租户](azure-stack-enable-multitenancy.md)你可以从远程目录租户订阅产品。

### <a name="to-subscribe-to-an-offer"></a>若要订阅产品
1. [登录](azure-stack-connect-azure-stack.md)至 Azure 堆栈用户门户 (https://portal.local.azurestack.external)，并选中**获取订阅**。

   ![获取订阅](media/azure-stack-subscribe-plan-provision-vm/image01.png)
2. 在**显示名称**字段中，键入你的订阅的名称，单击**提供**，单击一个产品/服务**选择提议**窗格中，，然后单击**创建**。

   ![创建产品](media/azure-stack-subscribe-plan-provision-vm/image02.png)
3. 若要查看你创建的订阅，请单击**更多的服务**，单击**订阅**，然后单击你的新订阅。  

在订阅产品/服务后，刷新门户后，若要确定哪些服务正在新订阅的一部分。

### <a name="to-subscribe-to-an-add-on-plan"></a>订阅外接程序计划
如果某项服务具有的外接程序计划，你可以将该计划添加到你的订阅在任何时间。  

1. 在用户门户中，选择**更多的服务** > **订阅**，然后选择你想更改的订阅。

2. 选择**添加计划**按钮，，然后选择所需的外接程序计划。 如果**添加计划**与该订阅不可用，然后没有外接程序计划提议关联。



## <a name="next-steps"></a>后续步骤
[预配虚拟机](azure-stack-provision-vm.md)
