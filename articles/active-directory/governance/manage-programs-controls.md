---
title: 管理程序的访问评审的 Azure Active Directory 的控件 |Microsoft Docs
description: 了解如何在您的组织收集和组织，Azure Active Directory 访问评审作为控件中创建其他计划，以便每个监管、 风险管理和符合性方案。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43c0f1c041bfed1b041a9926efd869d167c6f1e9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60246036"
---
# <a name="manage-programs-and-controls-for-azure-ad-access-reviews"></a>管理程序和控件的 Azure AD 访问评审

Azure Active Directory (Azure AD) 包括组成员和应用程序访问权限的访问评审。 这些示例控件可确保对有权访问组织的组成员资格和应用程序的用户进行有效监督。 组织可以使用这些控件有效地解决其监管、风险管理和合规性要求。

## <a name="create-and-manage-programs-and-their-controls"></a>创建和管理计划及其控件
可以将访问评审组织为计划，以便针对不同目的简化跟踪和收集访问评审的方式。 可以将每个访问评审链接到一个计划。 然后，在为审核员准备报告时，可以将重点放在特定计划范围内的访问评审。  程序和访问评审结果是对全局管理员、 用户管理员、 安全管理员或安全读取者角色中的用户可见。

若要查看计划列表，请转到[访问评审页](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)，选择“计划”。

“默认计划”将始终存在。 如果你是全局管理员或用户管理员角色中，您可以创建其他计划。 例如，可以选择针对每个符合性措施或业务目标创建一个计划。

如果不再需要某个计划，并且没有任何链接到它的控件，则可以将其删除。

## <a name="next-steps"></a>后续步骤

- [创建组或应用程序的访问评审](create-access-review.md)
- [检索组或应用程序的访问评审结果](retrieve-access-review.md)
