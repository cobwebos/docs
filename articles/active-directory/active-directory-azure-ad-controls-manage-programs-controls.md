---
title: "管理 Azure AD 访问评审的程序和控件| Microsoft Docs"
description: "可以为组织中每个监管、风险管理和合规性计划创建其他程序，以便以控件形式收集并组织 Azure Active Directory 访问评审。"
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: f6d7c2a1a3e3bf78b568f2f6b079476e403b9911
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="manage-programs-and-their-controls"></a>管理程序及其控件 

Azure AD 包括组成员和应用程序访问权限的访问评审，这些访问评审是确保监督谁有权访问的控件示例。 组织使用这些控件可以有效地解决其监管、风险管理和合规性要求。  

## <a name="how-to-manage-programs-and-their-controls"></a>如何管理程序及其控件
可以将访问评审组织为程序，以便针对不同目的简化访问评审的跟踪和收集。  每个访问评审都可链接到程序，以便在为审核员准备报告时，只有特定计划范围内的这些访问评审可见。

若要查看程序列表，请转到[“访问评审”页](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)并更改为“程序”选项卡。

有一个程序（即“默认程序”）始终存在。  如果拥有全局管理员角色，则可以创建其他程序。  例如，可以选择为每个合规性计划或业务目标创建一个程序。

如果不再需要某个程序，并且没有任何控件链接到它，则可以将其删除。

## <a name="next-steps"></a>后续步骤

- [创建对组成员的访问评审或对应用程序的访问](active-directory-azure-ad-controls-create-access-review.md)

