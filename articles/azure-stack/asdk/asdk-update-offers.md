---
title: "在本教程中，你将学习如何更新计划和 Azure 堆栈提供 |Microsoft 文档"
description: "本文介绍如何查看和修改现有 Azure 堆栈产品/服务和计划。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 3b5d2ab5e924f578f5838d11b0d2058aacf67dec
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-update-offers-and-plans"></a>教程： 更新产品/服务和计划
作为 Azure 堆栈操作员，你可以创建包含所需的服务和为你的用户订阅的适用配额的计划。 这些*基本计划*包含要向你的用户提供的核心服务，并且只能有一个基本计划，每个产品/服务。 如果你需要修改你的优惠，则可以使用*外接程序计划*，允许你修改计划以扩展计算机，存储，或网络最初提供与基本计划的配额。 

虽然一个计划将合并所有内容可能是最佳在某些情况下，你可能想要具有基本计划，并提供其他服务使用外接程序计划。 例如，你可以决定具有视为外接程序计划的所有 PaaS 服务属于基本计划，提供 IaaS 服务。 计划还可用来控制对有限 ASDK 环境中的资源的消耗。 例如，如果你希望用户要注意其资源使用情况，您可以相对较小的基本计划 （具体取决于所需的服务） 和用户达到容量，如将它们已使用的资源分配警报基于其分配计划。 在这里，用户可以选择其他资源的可用外接程序计划。 

> [!NOTE]
> 当用户将添加到现有产品/服务订阅的外接程序计划时，额外的资源可能花费一个小时的时间才会显示。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建外接程序计划 
> * 订阅外接程序计划

## <a name="create-an-add-on-plan"></a>创建外接程序计划
**外接程序计划**创建通过修改现有提议。

1. 登录到[Azure 堆栈门户](https://adminportal.local.azurestack.external)作为云管理员联系。
2. 按照相同步骤以前用于[创建基本计划](asdk-offer-services.md)以创建新的计划提供以前未提供的服务。 在此示例中，将在计划中包括密钥保管库 (Microsoft.KeyVault) 服务。
3. 在管理员门户中，单击**提供**，然后选择要与外接程序计划更新的产品。

   ![](media/asdk-update-offers/1.PNG)

4.  滚动到底部的优惠属性并选择**外接程序计划**。 单击 **“添加”**。
   
    ![](media/asdk-update-offers/2.PNG)

5. 选择要添加的计划。 在此示例中，计划称为**密钥保管库计划**，然后单击**选择**将计划添加到产品/服务。 你应该会收到通知已成功添加到产品/服务计划。
   
    ![](media/asdk-update-offers/3.PNG)

6. 查看计划提供验证的新外接程序计划包括列出的外接程序的列表。
   
    ![](media/asdk-update-offers/4.PNG)

## <a name="subscribe-to-the-add-on-plan"></a>订阅外接程序计划
你需要登录到 Azure 堆栈用户门户，以便扩展的外接程序计划的现有 Azure 堆栈订阅。

使用以下步骤来发现向提供了通过 Azure 堆栈运算符的其他资源并向你先前订阅产品/服务添加外接程序计划。

1. 登录到[用户门户](https://portal.local.azurestack.external)。
2. 若要查找要使用外接程序计划扩展的订阅，单击**更多的服务**，单击**订阅**，然后选择你的订阅。
   
    ![](media/asdk-update-offers/5.PNG)

3.  在订阅概述中，单击**添加计划**。
   
    ![](media/asdk-update-offers/6.PNG)

4. 在添加计划的边栏选项卡，选择要添加到订阅的外接程序计划。 在此示例中，**密钥保管库计划**选择。 你随后应收到通知外接程序计划已成功获取并需要刷新门户后，无法访问该更新的订阅。
   
    ![](media/asdk-update-offers/7.PNG)

5. 最后，查看与订阅关联的外接程序计划以确保已成功添加外接程序计划。
   
    ![](media/asdk-update-offers/8.PNG)


## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 创建外接程序计划 
> * 订阅外接程序计划

> [!div class="nextstepaction"]
> [从模板创建虚拟机](asdk-create-vm-template.md)

