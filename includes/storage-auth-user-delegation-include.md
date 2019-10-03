---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/25/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: f3b9f6c27fb8d423350eac5d286c9859ad6fbd37
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70104223"
---
## <a name="about-the-user-delegation-sas-preview"></a>关于用户委托 SAS (预览版)

可使用 Azure AD 凭据或帐户密钥来保护对容器或 blob 的访问的 SAS 令牌。 使用 Azure AD 凭据保护的 SAS 称为用户委托 SAS, 因为用于为 SAS 签署的 OAuth 2.0 令牌是代表用户请求的。

Microsoft 建议尽可能使用 Azure AD 凭据作为最佳安全方案, 而不是使用帐户密钥, 这样可以更容易地受到威胁。 当应用程序设计要求使用共享访问签名时, 请使用 Azure AD 凭据创建用户委托 SAS 以实现高级安全性。 有关用户委托 SAS 的详细信息, 请参阅[创建用户委托 sas](/rest/api/storageservices/create-user-delegation-sas)。

> [!NOTE]
> 用户委托 SAS 预览版仅适用于非生产使用。

> [!CAUTION]
> 拥有有效 SAS 的任何客户端都可以访问该 SAS 允许的存储帐户中的数据。 保护 SAS 不受恶意或无意使用非常重要。 请在分发 SAS 时使用判断, 并制定一个准备吊销已泄露 SAS 的计划。

有关共享访问签名的详细信息, 请参阅[使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](../articles/storage/common/storage-sas-overview.md)。
