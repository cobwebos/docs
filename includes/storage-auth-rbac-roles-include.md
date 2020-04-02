---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/17/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d73bab76860abf3e9fa442dad44e1ddb66d147a3
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519548"
---
Azure 提供以下内置 RBAC 角色，用于授权使用 Azure AD 和 OAuth 访问 blob 和队列数据：

- [存储 Blob 数据所有者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner)：用于设置 Azure 数据湖存储 Gen2 的所有权和管理 POSIX 访问控制。 有关详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](../articles/storage/blobs/data-lake-storage-access-control.md)。
- [存储 Blob 数据参与者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor)：用于向 Blob 存储资源授予读取/写入/删除权限。
- [存储 Blob 数据读取器](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader)：用于向 Blob 存储资源授予只读权限。
- [存储队列数据参与者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor)：用于向 Azure 队列授予读取/写入/删除权限。
- [存储队列数据读取器](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader)：用于向 Azure 队列授予只读权限。
- [存储队列数据消息处理器](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor)：用于向 Azure 存储队列中的消息授予查看、检索和删除权限。
- [存储队列数据消息发件人](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender)：用于向 Azure 存储队列中的邮件授予添加权限。

有关用于数据服务和管理服务的 Azure 存储的内置 RBAC 角色的详细信息，请参阅[Azure RBAC 内置角色](../articles/role-based-access-control/built-in-roles.md#storage)中的**存储**部分。 此外，有关在 Azure 中提供权限的不同类型的角色的信息，请参阅[经典订阅管理员角色、Azure RBAC 角色和 Azure AD 角色](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)。

> [!NOTE]
> RBAC 角色分配可能需要最多五分钟的时间进行传播。
>
> 只有为数据访问显式定义的角色才允许安全主体访问 blob 或队列数据。 **所有者**、**参与者**和**存储帐户参与者**等角色允许安全主体管理存储帐户，但不提供对该帐户中 blob 或队列数据的访问。
>
> 可以使用 Azure AD 帐户或存储帐户访问密钥授权访问 Azure 门户中的 Blob 或队列数据。 有关详细信息，请参阅[使用 Azure 门户访问 blob 或队列数据](../articles/storage/common/storage-access-blobs-queues-portal.md)。
