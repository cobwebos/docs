---
title: 如何更新 Azure Stack 套餐和计划 | Microsoft Docs
description: 本文介绍如何查看和修改现有的 Azure Stack 套餐和计划。
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
ms.date: 07/30/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: a35ba993e6fd1162fa4a18bc0d6bc9351fe7dfa2
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358246"
---
# <a name="azure-stack-add-on-plans"></a>Azure Stack 附加计划

Azure Stack 操作员创建加载项计划来修改[*基本计划*](azure-stack-create-plan.md)当你想要提供其他服务或扩展*计算机*，*存储*，或*网络*超出基本计划初始产品/服务的配额。 加载项计划修改基本计划，用户可以选择要订阅的可选扩展。 

有些的时候是最佳的所有内容组合在单个计划。 其他情况下您可能想先制定一个基本计划，然后使用附加计划提供的其他服务。 例如，可以通过基本计划提供 IaaS 服务，通过附加计划提供所有 PaaS 服务。

若要使用加载项计划的另一个原因是帮助用户留意其资源使用情况。 若要执行此操作，无法开始包含相对较小的配额 （具体取决于所需的服务） 的基本计划。 然后，在用户达到容量，它们将会向你发出警报，它们已使用基于其计划分配给资源的分配。 在这里，用户然后可以选择提供了其他资源的加载项计划。

> [!NOTE]
> 如果不希望使用加载项计划来扩展配额，你还可以选择[编辑原始配置的配额](azure-stack-quota-types.md#to-edit-a-quota)。 

当用户将附加计划添加到现有的套餐订阅时，可能需要长达一小时的时间才会显示附加资源。 

## <a name="create-an-add-on-plan"></a>创建附加计划
修改现有的套餐即可创建附加计划：

1. 以云管理员身份登录到 Azure Stack 管理员门户。
2. 遵循[创建基本计划](azure-stack-create-plan.md)的步骤，创建此前尚未提供的新计划套餐服务。 在此示例中，Key Vault (Microsoft.KeyVault) 服务将包含在新计划中。
3. 在管理员门户中单击“套餐”，然后选择需使用附加计划更新的套餐。

   ![](media/create-add-on-plan/1.PNG)

4.  滚动到套餐属性的底部，选择“附加计划”。 单击 **“添加”**。
   
    ![](media/create-add-on-plan/2.PNG)

5. 选择要添加的计划（ 在此示例中，该计划称为**密钥保管库计划**。 选择计划之后, 单击**选择**将计划添加到产品/服务。 系统会发送通知，告知你计划已成功添加到套餐中。
   
    ![](media/create-add-on-plan/3.PNG)

6. 查看随套餐提供的附加计划的列表，验证新附加计划是否已列出。
   
    ![](media/create-add-on-plan/4.PNG)

## <a name="next-steps"></a>后续步骤

  [创建产品/服务](azure-stack-create-offer.md)