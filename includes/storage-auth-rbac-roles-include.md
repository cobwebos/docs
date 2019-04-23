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
ms.openlocfilehash: 026717dff2b6883eb643497dec91226e4afe8133
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2019
ms.locfileid: "60150211"
---
Azure 提供了以下内置 RBAC 角色授予对 blob 和队列数据使用 Azure AD 访问权限和 OAuth:

- [存储 Blob 数据所有者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner):用来为 Azure Data Lake Storage Gen2（预览版）设置所有权和管理 POSIX 访问控制。 有关详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](../articles/storage/blobs/data-lake-storage-access-control.md)。
- [存储 Blob 数据参与者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor):用来授予对 Blob 存储资源的读取/写入/删除权限。
- [存储 Blob 数据读取器](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader):用来授予对 Blob 存储资源的只读权限。
- [存储队列数据参与者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor):用来授予对 Azure 队列的读取/写入/删除权限。
- [存储队列数据读取器](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader):用来授予对 Azure 队列的只读权限。
- [存储队列数据消息处理器](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor):用于对授予扫视、 检索、 和到 Azure 存储队列中的消息的删除权限。
- [存储队列数据消息发件人](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender):用于授予将权限添加到 Azure 存储队列中的消息。

> [!NOTE]
> 请记住，RBAC 角色分配可能需要五分钟的时间来传播。
