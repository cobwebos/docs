---
title: "如何使用 Azure AD 访问评审来评审访问权限 | Microsoft Docs"
description: "如何使用 Azure AD 访问评审来评审访问权限。"
services: active-directory
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
ms.openlocfilehash: c9115bb5bc3d22c67ea0ac77b8972ae7f919926b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-review-access-with-azure-ad-access-reviews"></a>如何使用 Azure AD 访问评审来评审访问权限

Azure Active Directory 使用称为“访问评审”的功能，简化了企业对 Azure AD 及其他 Microsoft Online Services 中应用程序和组成员的访问的管理方式。  你可能收到过来自 Microsoft 的电子邮件，要求你评审访问权限 - 有权访问应用程序的组成员或用户。 

## <a name="opening-an-access-review"></a>打开访问评审

可以通过单击电子邮件中的链接来查看未完成的访问评审。  如果没有电子邮件，则可以通过执行以下操作来找到访问评审：

1. 登录到 [Azure AD 访问面板](https://myapps.microsoft.com)。
2. 单击页面右上角的用户图标，其中显示名称和默认组织。 如果列出多个组织，请选择已请求访问评审的组织。
3. 如果页面右侧有标记为“访问评审”的磁贴，请单击它。 否则，如果该磁贴不可见，则该组织没有要执行的访问评审，此时不需要执行任何操作。

## <a name="filling-out-an-access-review"></a>填写访问评审

从列表中选择访问评审，将看到需要评审的用户的名称。  如果该请求是要评审你自己的访问权限，则可能只看到一个名称（即你自己）。

对于列表中的每一行，你都可以决定是批准还是拒绝该用户的访问权限。 可以单击该行，选择是要批准或还是要拒绝。 （如果不认识该用户，也可以就此答复他。）

审阅者可能要求你提供批准继续访问或组成员身份的理由。

## <a name="next-steps"></a>后续步骤

请注意，不会立即删除用户被拒绝的访问权限，可以在评审完成或管理员停止评审时删除。 因此，如果想要更改回答和批准之前拒绝的用户，或拒绝之前批准的用户，则可以单击对应的行，重置答复并选取新答复。  在访问评审完成之前，可以执行此操作。



