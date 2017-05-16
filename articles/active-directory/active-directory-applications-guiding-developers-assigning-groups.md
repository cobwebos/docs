---
title: "将组分配到 Azure AD 应用 | Microsoft 文档"
description: "如何为 Azure 应用程序实现组分配。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: 29b5ba89-a1c7-4f1f-a294-248a40106617
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
robots: noindex
ms.translationtype: Human Translation
ms.sourcegitcommit: 015cc28903bfd366c653a51b0f73512bf8b578ea
ms.openlocfilehash: f58c051bc25544d2811738b8ade483c82f3901b2
ms.contentlocale: zh-cn
ms.lasthandoff: 02/28/2017

---
# <a name="assign-azure-active-directory-groups-to-an-application"></a>将 Azure Active Directory 组分配到应用程序
在将用户和组分配到应用程序之前，你必须要求用户分配。 若要了解如何要求进行用户分配，请参阅[要求进行用户分配](active-directory-applications-guiding-developers-requiring-user-assignment.md)一文。

本文假设你已经在用于此应用程序的 Active Directory 中创建了组。

## <a name="assigning-groups-to-an-application"></a>将组分配到应用程序
1. 使用管理员帐户登录到 Azure 门户。
2. 在主菜单中单击“所有项”。
3. 选择要用于应用程序的目录。
4. 单击“应用程序”选项卡。
5. 从与此目录关联的应用程序列表中选择应用程序。
6. 单击“用户和组”选项卡。
7. 通过使用“组”下拉列表，筛选 Active Directory 中的组列表。
8. 选择组。
9. 单击“分配”。
10. 出现提示时单击“是”。

## <a name="next-steps"></a>后续步骤
[!INCLUDE [active-directory-applications-guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]

