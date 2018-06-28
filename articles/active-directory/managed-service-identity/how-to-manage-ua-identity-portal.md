---
title: 如何使用 Azure 门户管理用户分配的托管服务标识 (MSI)
description: 分步说明如何创建、列出和删除用户分配的托管服务标识。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: 206f508ca8df13cdc8194cc9a95d828397ea0b85
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2018
ms.locfileid: "33929346"
---
# <a name="create-list-or-delete-a-user-assigned-managed-service-identity-msi-using-the-azure-portal"></a>使用 Azure 门户创建、列出或删除用户分配的托管服务标识 (MSI)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

托管服务标识在 Azure Active Directory 中为 Azure 服务提供托管标识。 此标识可用于向支持 Azure AD 身份验证的服务进行身份验证，这样就无需在代码中输入凭据了。 

在此期间，不能使用 Azure 门户创建、列出或删除用户分配的托管标识。  稍后返回查看更新。