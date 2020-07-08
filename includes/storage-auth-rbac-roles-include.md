---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b49d757067d8ee888bee52f3931c8d48d786c044
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024874"
---
Azure 提供以下内置 RBAC 角色，用于授权使用 Azure AD 和 OAuth 访问 blob 和队列数据：

- [存储 Blob 数据所有者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner)：用于为 Azure Data Lake Storage Gen2 设置所有权和管理 POSIX 访问控制。 有关详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](../articles/storage/blobs/data-lake-storage-access-control.md)。
- [存储 Blob 数据参与者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor)：用来授予对 Blob 存储资源的读取/写入/删除权限。
- [存储 Blob 数据读取者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader)：用来授予对 Blob 存储资源的只读权限。
- [存储 Blob 代理](../articles/role-based-access-control/built-in-roles.md#storage-blob-delegator)：获取用于创建共享访问签名的用户委托密钥，该签名使用容器或 Blob Azure AD 凭据进行签名。
- [存储队列数据参与者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor)：用来授予对 Azure 队列的读取/写入/删除权限。
- [存储队列数据读取者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader)：用来授予对 Azure 队列的只读权限。
- [存储队列数据消息处理者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor)：用来对 Azure 存储队列中的消息授予扫视、检索和删除权限。
- [存储队列数据消息发送者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender)：用来对 Azure 存储队列中的消息授予添加权限。

只有为数据访问显式定义的角色才允许安全主体访问 blob 或队列数据。 "**所有者**"、"**参与者**" 和 "**存储帐户参与者**" 等内置角色允许安全主体管理存储帐户，但不通过 Azure AD 提供对该帐户中的 blob 或队列数据的访问。 但是，如果角色包含 storageAccounts/ **/listKeys/action**，则分配有该角色的用户可以通过使用帐户访问密钥进行共享密钥授权来访问存储帐户中的数据。 有关详细信息，请参阅[使用 Azure 门户访问 blob 或队列数据](../articles/storage/common/storage-access-blobs-queues-portal.md)。

要详细了解用于数据服务和管理服务的 Azure 存储的内置 RBAC 角色，请参阅[用于 Azure RBAC 的 Azure 内置角色](../articles/role-based-access-control/built-in-roles.md#storage)中的“存储”部分。 此外，要了解在 Azure 中提供权限的不同类型角色，请参阅[经典订阅管理员角色、Azure RBAC 角色和 Azure AD 角色](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)。

> [!IMPORTANT]
> RBAC 角色分配可能需要最多五分钟的时间进行传播。
