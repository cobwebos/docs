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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "76279303"
---
Azure 提供以下内置 RBAC 角色，用于授权使用 Azure AD 和 OAuth 访问 blob 和队列数据：

- [存储 Blob 数据所有者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner)：用于设置 Azure 数据湖存储 Gen2 的所有权和管理 POSIX 访问控制。 有关详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](../articles/storage/blobs/data-lake-storage-access-control.md)。
- [存储 Blob 数据参与者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor)：用于向 Blob 存储资源授予读取/写入/删除权限。
- [存储 Blob 数据读取器](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader)：用于向 Blob 存储资源授予只读权限。
- [存储队列数据参与者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor)：用于向 Azure 队列授予读取/写入/删除权限。
- [存储队列数据读取器](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader)：用于向 Azure 队列授予只读权限。
- [存储队列数据消息处理器](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor)：用于向 Azure 存储队列中的消息授予查看、检索和删除权限。
- [存储队列数据消息发件人](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender)：用于向 Azure 存储队列中的邮件授予添加权限。

> [!NOTE]
> RBAC 角色分配可能需要最多五分钟的时间进行传播。
>
> 只有为数据访问显式定义的角色才允许安全主体访问 blob 或队列数据。 **所有者**、**参与者**和**存储帐户参与者**等角色允许安全主体管理存储帐户，但不提供对该帐户中 blob 或队列数据的访问。
>
> 可以使用 Azure AD 帐户或存储帐户访问密钥授权访问 Azure 门户中的 Blob 或队列数据。 有关详细信息，请参阅[使用 Azure 门户访问 blob 或队列数据](../articles/storage/common/storage-access-blobs-queues-portal.md)。
