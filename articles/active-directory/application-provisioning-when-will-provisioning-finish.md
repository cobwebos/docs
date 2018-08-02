---
title: 用户预配到 Azure AD 库应用程序需要数小时或更长时间 | Microsoft Docs
description: 如何找出预配到应用程序时所需时间可能会比预期长的原因
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 5be6933c4cd9efa4b8decc4cba6298f18610266a
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364691"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>用户预配到 Azure AD 库应用程序需要数小时或更长时间

首次为应用程序启用自动预配时，初始同步可能会持续 20 分钟至几小时，具体取决于 Azure AD 目录的大小与作用域中的预配用户数量。 

在初始同步之后同步速度会变快，因为在初始同步后，预配服务已储存了代表两个系统状态的水印，提高了之后的同步性能。

## <a name="how-to-improve-provisioning-performance"></a>如何提高预配性能

如果初始同步所需时间不止几个小时，可执行以下操作来提高性能：

-   **用户范围筛选器** 使用范围筛选器，可优化预配服务通过筛选用户（基于特定属性值）从 Azure AD 提取的数据。 有关范围筛选器的详细信息，请参阅[使用范围筛选器进行基于属性的应用程序预配](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters)。

## <a name="next-steps"></a>后续步骤
[Azure Active Directory SaaS 应用程序的自动化用户预配和取消预配](active-directory-saas-app-provisioning.md)

