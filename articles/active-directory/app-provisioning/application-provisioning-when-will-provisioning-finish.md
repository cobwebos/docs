---
title: 用户预配到 Azure AD 库应用需要数小时或更长时间
description: 如何找出预配到应用程序时所需时间可能会比预期长的原因
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.openlocfilehash: d663962dab058a108faa7d5310a3f2892c345a75
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82593890"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>用户预配到 Azure AD 库应用程序需要数小时或更长时间

首次为应用程序启用自动预配时，初始周期可能需要20分钟到几小时的时间，具体取决于 Azure AD 目录的大小以及用于预配的范围内的用户数量。 

初始循环后的后续同步速度更快，因为预配服务在初始周期后存储表示这两个系统状态的水印，从而提高后续同步的性能。

## <a name="how-to-improve-provisioning-performance"></a>如何提高预配性能

如果初始周期花费了几个小时，则可以执行以下操作来提高性能：

-   **用户范围筛选器** 使用范围筛选器，可优化预配服务通过筛选用户（基于特定属性值）从 Azure AD 提取的数据。 有关作用域筛选器的详细信息，请参阅[通过作用域筛选器基于属性预配应用程序](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。

## <a name="next-steps"></a>后续步骤
[Azure Active Directory SaaS 应用程序的自动化用户设置和取消设置](user-provisioning.md)

