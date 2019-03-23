---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/21/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 50217829be7aff3bcdbf417f19fff9f1fd7c6901
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372656"
---
> [!NOTE]
> - RBAC 角色分配可能需要最多两分钟来传播。
>
> 要使用 OAuth 令牌授权 blob 和队列操作，必须使用 HTTPS。
>
> - Azure 门户现支持使用 Azure AD 凭据来读取和写入 blob 和队列数据，这是预览版本中的一部分。 若要访问 Azure 门户中的 blob 和队列数据，必须将用户分配 Azure 资源管理器**读取器**角色，除了 blob 或队列的访问权限的相应角色。 有关详细信息，请参阅[授予对 Azure 容器和队列使用 RBAC 在 Azure 门户中的访问](../articles/storage/common/storage-auth-aad-rbac.md)。 
> 
> - [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)当前使用存储帐户密钥访问 blob 和队列数据。 如果该密钥不可用，则 Azure AD 授权用于 blob 的访问权限。 Azure AD 授权当前不支持对队列中。 
>
> - Azure 文件仅支持使用 Azure AD 通过 SMB 进行身份验证，以用于加入域的 VM（预览版）。 若要了解有关在 Azure 文件中通过 SMB 使用 Azure AD 的信息，请参阅[通过 SMB 对 Azure 文件进行 Azure Active Directory身份验证概述（预览版）](../articles/storage/files/storage-files-active-directory-overview.md)。