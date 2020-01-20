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
ms.openlocfilehash: 4b10955a1d3b85acbcae109836bebc03ec04c72c
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279303"
---
Azure 提供下列内置 RBAC 角色，用于授权使用 Azure AD 和 OAuth 访问 blob 和队列数据：

- [存储 Blob 数据所有者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner)：用于设置对 Azure Data Lake Storage Gen2 的所有权和管理 POSIX 访问控制。 有关详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](../articles/storage/blobs/data-lake-storage-access-control.md)。
- [存储 Blob 数据参与者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor)：用于授予对 Blob 存储资源的读取/写入/删除权限。
- [存储 Blob 数据读取器](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader)：用于向 Blob 存储资源授予只读权限。
- [存储队列数据参与者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor)：用于授予对 Azure 队列的读取/写入/删除权限。
- [存储队列数据读取器](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader)：用于向 Azure 队列授予只读权限。
- [存储队列数据消息处理器](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor)：用于授予对 Azure 存储队列中的消息的查看、检索和删除权限。
- [存储队列数据消息发送方](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender)：用于授予对 Azure 存储队列中的消息的添加权限。

> [!NOTE]
> RBAC 角色分配最多可能需要五分钟才能传播。
>
> 仅对数据访问显式定义的角色允许安全主体访问 blob 或队列数据。 **所有者**、**参与者**和**存储帐户参与者**等角色允许安全主体来管理存储帐户，但不提供对该帐户内的 blob 或队列数据的访问权限。
>
> 可以使用你的 Azure AD 帐户或存储帐户访问密钥来授权 Azure 门户中对 blob 或队列数据的访问。 有关详细信息，请参阅[使用 Azure 门户访问 blob 或队列数据](../articles/storage/common/storage-access-blobs-queues-portal.md)。
