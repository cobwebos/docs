---
title: "管理 Azure AD 访问评审的程序和控件| Microsoft Docs"
description: "可以为组织中每个监管、风险管理和合规性计划创建其他计划，以便以控件形式收集并组织 Azure Active Directory 访问评审。"
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: f322bff427384e6432fcc0c288704f6bb605c900
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="manage-programs-and-their-controls"></a>管理程序及其控件 

Azure Active Directory (Azure AD) 包括组成员和应用程序访问权限的访问评审。 这些示例控件可确保对有权访问组织的组成员资格和应用程序的用户进行有效监督。 组织可以使用这些控件有效地解决其监管、风险管理和合规性要求。

## <a name="create-and-manage-programs-and-their-controls"></a>创建和管理计划及其控件
可以将访问评审组织为计划，以便针对不同目的简化跟踪和收集访问评审的方式。 可以将每个访问评审链接到一个计划。 当准备报表以供审核员进行审核时，将仅显示属于特定目的范围的访问评审。

若要查看计划列表，请转到[访问评审页](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)，选择“计划”。

“默认计划”将始终存在。 如果拥有全局管理员角色，则可以创建其他计划。 例如，可以选择针对每个符合性措施或业务目标创建一个计划。

如果不再需要某个计划，并且没有任何链接到它的控件，则可以将其删除。

## <a name="next-steps"></a>后续步骤

[创建对组成员的访问评审或对应用程序的访问](active-directory-azure-ad-controls-create-access-review.md)

