---
title: 要求进行用户分配 - Azure AD | Microsoft 文档
description: 如何要求对 Azure 应用程序进行用户分配。
services: active-directory
documentationcenter: ''
author: kgremban
manager: mtillman
editor: ''
ms.assetid: 30b78cba-1e0f-472f-8314-f2250a9b91c3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: kgremban
robots: noindex
ms.openlocfilehash: 4519681d9b91383d27c00a992f85b0cb5d74f235
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2018
ms.locfileid: "37859456"
---
# <a name="azure-ad-and-applications-require-user-assignment"></a>Azure AD 和应用程序：要求进行用户分配
## <a name="requiring-user-assignment"></a>要求进行用户分配
1. 使用管理员帐户登录到 Azure 门户。
2. 在主菜单中单击“所有服务”项。
3. 选择要用于应用程序的目录。
4. 单击“企业应用程序”选项卡。
5. 从与此目录关联的应用程序列表中选择应用程序。
6. 单击“属性”选项卡。
7. 将“需要进行用户分配?”切换为“是”。
8. 单击屏幕顶部的“保存”按钮。

现在，必须将用户和/或组分配到应用程序。 请参阅[将用户分配到应用程序](active-directory-applications-guiding-developers-assigning-users.md)和[将组分配到应用程序](active-directory-applications-guiding-developers-assigning-groups.md)。

## <a name="next-steps"></a>后续步骤
[!INCLUDE [active-directory-applications-guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]
