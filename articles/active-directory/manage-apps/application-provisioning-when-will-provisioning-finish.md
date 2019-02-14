---
title: 用户预配到 Azure AD 库应用程序需要数小时或更长时间 | Microsoft Docs
description: 如何找出预配到应用程序时所需时间可能会比预期长的原因
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 834e2532354b91410943f5fe2b2e78bca0bd0922
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56173039"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>用户预配到 Azure AD 库应用程序需要数小时或更长时间

首次为应用程序启用自动预配时，初始同步可能会持续 20 分钟至几小时，具体取决于 Azure AD 目录的大小与作用域中的预配用户数量。 

在初始同步之后同步速度会变快，因为在初始同步后，预配服务已储存了代表两个系统状态的水印，提高了之后的同步性能。

## <a name="how-to-improve-provisioning-performance"></a>如何提高预配性能

如果初始同步所需时间不止几个小时，可执行以下操作来提高性能：

-   **用户范围筛选器** 使用范围筛选器，可优化预配服务通过筛选用户（基于特定属性值）从 Azure AD 提取的数据。 有关范围筛选器的详细信息，请参阅[使用范围筛选器进行基于属性的应用程序预配](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters)。

## <a name="next-steps"></a>后续步骤
[通过 Azure Active Directory 应用程序为 SaaS 自动化用户预配和取消预配](user-provisioning.md)

