---
title: "在 Azure Active Directory 中管理组所有者 | Microsoft Docs"
description: "了解如何管理组所有者，以及如何使用这些组管理对资源的访问权限。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 44350a3c-8ea1-4da1-aaac-7fc53933dd21
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2017
ms.author: curtand
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 70be82fdd673c4f245e306b1e1cfdcd94d4dac53
ms.contentlocale: zh-cn
ms.lasthandoff: 09/14/2017

---
# <a name="managing-owners-for-a-group"></a>管理组的所有者
在资源所有者授予 Azure AD 组对资源的访问权限后，便由组所有者管理组的成员资格。 实际上，资源所有者是将其资源的用户访问权限委派给了组的所有者。

## <a name="add-an-owner-to-a-group"></a>向组添加所有者

1. 在 [Azure AD 管理中心](https://aad.portal.azure.com)内，选择“用户和组”。
2. 选择“所有组”，再打开要向其中添加所有者的组。
3. 依次选择“所有者”和“添加所有者”。
4. 在“添加所有者”页上，选择要添加为此组所有者的用户，再单击或点击“选择”。 

## <a name="remove-an-owner-from-a-group"></a>删除组所有者

1. 在 [Azure AD 管理中心](https://aad.portal.azure.com)内，选择“用户和组”。
2. 选择“所有组”，再打开要从中删除所有者的组。
3. 依次选择“所有者”和要从此组中删除的所有者，再单击或点击“删除”。
4. 在系统为选定所有者打开的窗格中，选择“删除”。

## <a name="additional-information"></a>其他信息
以下文章提供了有关 Azure Active Directory 组的更多信息。

* [查看现有组](active-directory-groups-view-azure-portal.md)
* [创建新组并添加成员](active-directory-groups-create-azure-portal.md)
* [管理组的设置](active-directory-groups-settings-azure-portal.md)
* [管理组的成员身份](active-directory-groups-membership-azure-portal.md)
* [管理组中用户的动态规则](active-directory-groups-dynamic-membership-azure-portal.md)

