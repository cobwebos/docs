---
title: 如何更新 Azure Stack 套餐和计划 | Microsoft Docs
description: 本文介绍如何查看和修改现有的 Azure Stack 套餐和计划。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.custom: mvc
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: c6b86a2cebccfde27841dca64cf24ca4f1be0f7b
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2019
ms.locfileid: "54247570"
---
# <a name="azure-stack-add-on-plans"></a>Azure Stack 附加计划

Azure Stack 操作员创建加载项计划来修改[基本计划](azure-stack-create-plan.md)当你想要提供其他服务或扩展*计算机*，*存储*，或者*网络*超出基本计划初始产品/服务的配额。 附加计划对基本计划进行修改，是用户可以选择订阅的可选扩展。

有些情况下，将所有内容组合到单个计划中是最好的。 另一些情况下，你可能希望使用基本计划，然后通过使用附加计划来提供附加服务。 例如，可以通过基本计划提供 IaaS 服务，通过附加计划提供所有 PaaS 服务。

使用附加计划的另一个原因是帮助用户记住其资源使用情况。 若要执行此操作，可以从一个包括相对较小的配额（取决于所需的服务）的基本计划开始。 然后，当用户达到容量时，将会向用户发送通知，指出他们已消耗完了根据其已分配计划分配的资源。 在这里，用户可以选择提供了其他资源的加载项计划。

> [!NOTE]
> 如果您不想要使用附加计划来扩展配额，你还可以选择[编辑原始配置的配额](azure-stack-quota-types.md#edit-a-quota)。

当用户将附加计划添加到现有产品/服务订阅时，额外的资源可能需要一小时才会显示。

## <a name="create-an-add-on-plan"></a>创建附加计划

修改现有的套餐即可创建附加计划：

1. 以云管理员身份登录到 Azure Stack 管理员门户。
2. 遵循[创建基本计划](azure-stack-create-plan.md)的步骤，创建此前尚未提供的新计划套餐服务。 在此示例中，密钥保管库 (**Microsoft.KeyVault**) 服务将包括在新的计划。
3. 在管理员门户中单击“套餐”，然后选择需使用附加计划更新的套餐。

   ![创建加载项计划](media/create-add-on-plan/1.PNG)

4. 滚动到套餐属性的底部，选择“附加计划”。 单击“添加”。

    ![创建加载项计划](media/create-add-on-plan/2.PNG)

5. 选择要添加的计划（ 在此示例中，计划名为“密钥保管库计划”。 选择计划后，单击“选择”将计划添加到套餐。 系统会发送通知，告知你计划已成功添加到套餐中。

    ![创建加载项计划](media/create-add-on-plan/3.PNG)

6. 查看产品/服务以验证列出了新的加载项计划中包含的附加计划的列表。

    ![创建加载项计划](media/create-add-on-plan/4.PNG)

## <a name="next-steps"></a>后续步骤

* [创建产品/服务](azure-stack-create-offer.md)