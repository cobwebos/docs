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
ms.openlocfilehash: 1fba8cc9ae40cf5539016bbd73de65f557a64136
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66111390"
---
> [!IMPORTANT]
> 在完成本文中的任务之前，请启用到 Azure AD 域服务的密码哈希同步。 
>
> 按照下面的说明进行操作，具体取决于 Azure AD 目录中的用户类型。 如果 Azure AD 目录中混合使用仅限云用户帐户和同步的用户帐户，请完成这两组操作。 您可能不能在你尝试使用 B2B 来宾帐户 （例如，你 gmail 或不同标识提供者，我们允许 MSA） 的情况下执行以下操作因为我们没有同步到托管域，因为这些这些用户的密码是在目录中的来宾帐户。 有关这些帐户，包括其密码的完整信息会在 Azure AD 外部，因为此信息不是在 Azure AD 中因此它不会不甚至获取同步到托管域。 
> - [面向仅限云用户帐户的说明](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [面向从本地目录同步的用户帐户的说明](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)
