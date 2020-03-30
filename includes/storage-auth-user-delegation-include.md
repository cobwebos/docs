---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/15/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a0a4a10797109cb3363027e2445259d06d9aa071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "76118076"
---
## <a name="about-the-user-delegation-sas"></a>关于用户委派 SAS

可以使用 Azure AD 凭据或帐户密钥保护用于访问容器或 Blob 的 SAS 令牌。 使用 Azure AD 凭据保护的 SAS 称为用户委派 SAS，因为用于对 SAS 进行签名的 OAuth 2.0 令牌是代表用户请求的。

Microsoft 建议您尽可能使用 Azure AD 凭据作为安全最佳做法，而不是使用帐户密钥，因为帐户密钥更易于泄露。 当应用程序设计需要共享访问签名时，请使用 Azure AD 凭据创建用户委派 SAS 以获得卓越的安全性。 有关用户委派 SAS 的详细信息，请参阅[创建用户委派 SAS](/rest/api/storageservices/create-user-delegation-sas)。

> [!CAUTION]
> 任何拥有有效 SAS 的客户端都可以在 SAS 允许的存储帐户中访问数据。 保护 SAS 免受恶意或意外使用非常重要。 在分发 SAS 时使用自由裁量权，并制定计划以撤销受攻击的 SAS。

有关共享访问签名的详细信息，请参阅[使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](../articles/storage/common/storage-sas-overview.md)。
