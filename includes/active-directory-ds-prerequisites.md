---
title: include 文件
description: 带有先决条件的 include 文件
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 66b5f8e2-e08d-43c8-b460-6204aecda8f7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.custom: include file
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: f7b1b294e9500ef9e0aadd24cfe3cd4e61fddda1
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68426851"
---
> [!IMPORTANT]
> 在完成本文中的任务之前，请启用到 Azure AD 域服务的密码哈希同步。
>
> 按照下面的说明进行操作，具体取决于 Azure AD 目录中的用户类型。 如果 Azure AD 目录中混合使用仅限云用户帐户和同步的用户帐户，请完成这两组操作。 如果尝试使用 B2B 来宾帐户 (例如, 来自我们所允许的其他标识提供者的 gmail 或 MSA), 则可能无法执行以下操作, 因为我们不会将这些用户的密码同步到托管域。是目录中的来宾帐户。 这些帐户的完整信息 (包括其密码) 将在 Azure AD 之外, 因为此信息不在 Azure AD 中, 因此它甚至不会同步到托管域。 
> - [面向仅限云用户帐户的说明](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [面向从本地目录同步的用户帐户的说明](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)
