---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/09/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 2d641287bcf095f13719667a91b7f61295309b5d
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430351"
---
> [!NOTE]
> - 适用于 blob 和队列的 Azure AD 身份验证预览版仅可用于非生产用途。 生产服务级别协议 (SLA) 当前不可用。 如果你的方案尚不支持 Azure AD 身份验证，请继续使用应用程序中的共享密钥授权或 SAS 令牌。
>
> - 预览期间，RBAC 角色分配可能需要最多五分钟的时间进行传播。
>
> - 当调用 blob 和队列操作时，必须使用 HTTPS 来向 Azure AD 证明身份。
>
> - Azure 门户现支持使用 Azure AD 凭据来读取和写入预览版本中的 blob 数据。
> 
> - Azure 门户当前不支持使用 Azure AD 凭据来读取和写入队列数据。 通过存储帐户密钥访问队列数据。
>
> - [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)当前使用存储帐户密钥访问 blob 和队列数据。
>
> - Azure 文件仅支持使用 Azure AD 通过 SMB 进行身份验证，以用于加入域的 VM（预览版）。 若要了解有关在 Azure 文件中通过 SMB 使用 Azure AD 的信息，请参阅[通过 SMB 对 Azure 文件进行 Azure Active Directory身份验证概述（预览版）](../articles/storage/files/storage-files-active-directory-overview.md)。



