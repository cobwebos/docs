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
ms.openlocfilehash: c0ca96d1829a73f856de021d1286e53007b03219
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87368961"
---
Azure 提供了以下 Azure 内置角色，用于使用 Azure AD 和 OAuth 授予对 Blob 和队列数据的访问权限：

- [存储 Blob 数据所有者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner)：用于为 Azure Data Lake Storage Gen2 设置所有权和管理 POSIX 访问控制。 有关详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](../articles/storage/blobs/data-lake-storage-access-control.md)。
- [存储 Blob 数据参与者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor)：用来授予对 Blob 存储资源的读取/写入/删除权限。
- [存储 Blob 数据读取者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader)：用来授予对 Blob 存储资源的只读权限。
- [存储 Blob 代理](../articles/role-based-access-control/built-in-roles.md#storage-blob-delegator)：获取用户委派密钥，用于创建使用 Azure AD 凭据为容器或 Blob 签名的共享访问签名。
- [存储队列数据参与者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor)：用来授予对 Azure 队列的读取/写入/删除权限。
- [存储队列数据读取者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader)：用来授予对 Azure 队列的只读权限。
- [存储队列数据消息处理者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor)：用来对 Azure 存储队列中的消息授予扫视、检索和删除权限。
- [存储队列数据消息发送者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender)：用来对 Azure 存储队列中的消息授予添加权限。

只有为数据访问明确定义的角色才允许安全主体访问 Blob 或队列数据。 内置角色（例如“所有者”、“参与者”和“存储帐户参与者”）允许安全主体管理存储帐户，但不通过 Azure AD 提供对该帐户内的 blob 或队列数据的访问权限  。 但是，如果角色包括 Microsoft.Storage/storageAccounts/listKeys/action，则获得了该角色的用户可以使用帐户访问密钥通过共享密钥授权来访问存储帐户中的数据。 有关详细信息，请参阅[使用 Azure 门户访问 Blob 或队列数据](../articles/storage/common/storage-access-blobs-queues-portal.md)。

要详细了解数据服务和管理服务的 Azure 存储的 Azure 内置角色，请参阅 [Azure RBAC 的 Azure 内置角色](../articles/role-based-access-control/built-in-roles.md#storage)的“存储”部分。 此外，要了解在 Azure 中提供权限的不同类型角色，请参阅[经典订阅管理员角色、Azure RBAC 角色和 Azure AD 角色](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)。

> [!IMPORTANT]
> RBAC 角色分配可能需要最多五分钟的时间进行传播。
