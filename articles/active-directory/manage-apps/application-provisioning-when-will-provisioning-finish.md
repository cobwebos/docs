---
title: 用户预配到 Azure AD 库应用需要数小时或更长时间
description: 如何找出预配到应用程序时所需时间可能会比预期长的原因
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d0c2586b129935043ae7b2eccd11cc3d65a385c
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712090"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>用户预配到 Azure AD 库应用程序需要数小时或更长时间

首次为应用程序启用自动预配时，初始周期可能需要20分钟到几小时的时间，具体取决于 Azure AD 目录的大小以及用于预配的范围内的用户数量。 

初始循环后的后续同步速度更快，因为预配服务在初始周期后存储表示这两个系统状态的水印，从而提高后续同步的性能。

## <a name="how-to-improve-provisioning-performance"></a>如何提高预配性能

如果初始周期花费了几个小时，则可以执行以下操作来提高性能：

-   **用户范围筛选器** 使用范围筛选器，可优化预配服务通过筛选用户（基于特定属性值）从 Azure AD 提取的数据。 有关作用域筛选器的详细信息，请参阅[通过作用域筛选器基于属性预配应用程序](define-conditional-rules-for-provisioning-user-accounts.md)。

## <a name="next-steps"></a>后续步骤
[通过 Azure Active Directory 应用程序为 SaaS 自动化用户预配和取消预配](user-provisioning.md)

