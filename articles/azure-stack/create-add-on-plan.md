---
title: 在本文中，你将了解如何更新计划和 Azure 堆栈提供 |Microsoft 文档
description: 本文介绍如何查看和修改现有的 Azure Stack 产品/服务和计划。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.custom: mvc
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: a84148a3ac31d51ff30cebffab00e5fec8fdaa87
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35238362"
---
# <a name="azure-stack-add-on-plans"></a>Azure 堆栈外接程序计划
作为 Azure Stack 操作员，你的责任是创建计划，提供适合用户订阅的服务和配额。 这些[*基本计划*](azure-stack-create-plan.md)包含要向你的用户提供的核心服务，并且只能有一个基本计划，每个产品/服务。 如需修改产品/服务，可以通过附加计划的方式来修改计划，扩大最初通过基本计划提供的计算机、存储或网络方面的配额。 

虽然在某些情况下通过单个计划将所有内容组合在一起是最理想的，但大多数情况下，需要先制定一个基本计划，然后以附加计划的方式提供其他服务。 例如，可以通过基本计划提供 IaaS 服务，通过附加计划提供所有 PaaS 服务。 计划还可用来控制 Azure 堆栈环境中的资源的消耗。 例如，如果希望用户留意其资源使用情况，可以制定一个相对较小的基本计划（具体取决于所需服务），让系统在用户达到容量限制时通知用户，告知他们已消耗按指定计划分配给他们的资源。 用户于是就会选择可用的附加计划以获取更多资源。 

> [!NOTE]
> 当用户将附加计划添加到现有的产品/服务订阅时，可能需要长达一小时的时间才会显示附加资源。 

## <a name="create-an-add-on-plan"></a>创建附加计划
通过修改现有提议创建外接程序计划：

1. 以云管理员身份登录到 Azure 堆栈管理员门户。
2. 按照相同步骤用于[创建新的基本计划](azure-stack-create-plan.md)以创建新的计划提供以前未提供的服务。 在此示例中，将新的计划中包括密钥保管库 (Microsoft.KeyVault) 服务。
3. 在管理员门户中单击“产品/服务”，然后选择需使用附加计划更新的产品/服务。

   ![](media/create-add-on-plan/1.PNG)

4.  滚动到产品/服务属性的底部，选择“附加计划”。 单击 **“添加”**。
   
    ![](media/create-add-on-plan/2.PNG)

5. 选择要添加的计划（ 在此示例中，该计划称为“密钥保管库计划”），然后单击“选择”，为产品/服务添加该计划。 系统会发送通知，告知你计划已成功添加到产品/服务中。
   
    ![](media/create-add-on-plan/3.PNG)

6. 查看随产品/服务提供的附加计划的列表，验证新附加计划是否已列出。
   
    ![](media/create-add-on-plan/4.PNG)

## <a name="next-steps"></a>后续步骤
[创建产品/服务](azure-stack-create-offer.md)