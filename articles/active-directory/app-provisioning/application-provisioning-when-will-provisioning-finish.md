---
title: 用户预配到 Azure AD 库应用需要数小时或更长时间
description: 如何找出预配到应用程序时所需时间可能会比预期长的原因
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: f0f572e5153b6e79bf7731d0d57b66e601a0054c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84781967"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>用户预配到 Azure AD 库应用程序需要数小时或更长时间

首次为应用程序启用自动预配时，初始周期可能需要20分钟到几小时的时间，具体取决于 Azure AD 目录的大小以及用于预配的范围内的用户数量。 

初始循环后的后续同步速度更快，因为预配服务在初始周期后存储表示这两个系统状态的水印，从而提高后续同步的性能。

## <a name="how-to-improve-provisioning-performance"></a>如何提高预配性能

如果初始周期花费了几个小时，则可以执行以下操作来提高性能：

-   **用户范围筛选器** 使用范围筛选器，可优化预配服务通过筛选用户（基于特定属性值）从 Azure AD 提取的数据。 有关作用域筛选器的详细信息，请参阅[通过作用域筛选器基于属性预配应用程序](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。

## <a name="next-steps"></a>后续步骤
[通过 Azure Active Directory 应用程序为 SaaS 自动化用户预配和取消预配](user-provisioning.md)

