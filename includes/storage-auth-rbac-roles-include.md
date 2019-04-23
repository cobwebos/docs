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
ms.openlocfilehash: 9b8418dba12748915666c6a91ee65b37c0f59ace
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59803388"
---
Azure 提供以下内置的 RBAC 角色，用于访问存储数据：

- [存储 Blob 数据所有者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner):用来为 Azure Data Lake Storage Gen2（预览版）设置所有权和管理 POSIX 访问控制。 有关详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](../articles/storage/blobs/data-lake-storage-access-control.md)。
- [存储 Blob 数据参与者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor):用来授予对 Blob 存储资源的读取/写入/删除权限。
- [存储 Blob 数据读取器](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader):用来授予对 Blob 存储资源的只读权限。
- [存储队列数据参与者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor):用来授予对 Azure 队列的读取/写入/删除权限。
- [存储队列数据读取器](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader):用来授予对 Azure 队列的只读权限。
- [存储队列数据消息处理器](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor):用于对授予扫视、 检索、 和到 Azure 存储队列中的消息的删除权限。
- [存储队列数据消息发件人](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender):用于授予将权限添加到 Azure 存储队列中的消息。

> [!IMPORTANT]
> RBAC 角色分配可能需要五分钟来传播。

有关如何为 Azure 存储定义内置角色的详细信息，请参阅[了解角色定义](../articles/role-based-access-control/role-definitions.md#management-and-data-operations-preview)。 若要了解如何创建自定义 RBAC 角色，请参阅[针对 Azure 基于角色的访问控制创建自定义角色](../articles/role-based-access-control/custom-roles.md)。 
