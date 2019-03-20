---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/19/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 9402c4b24c9d64b4b69d750fbd19de40cda396f3
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/01/2019
ms.locfileid: "57218084"
---
Azure 提供了用于访问存储的数据的以下内置 RBAC 角色：

- [存储 Blob 数据所有者（预览版）](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner-preview)：用来为 Azure Data Lake Storage Gen2（预览版）设置所有权和管理 POSIX 访问控制。 有关详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](../articles/storage/blobs/data-lake-storage-access-control.md)。
- [存储 Blob 数据参与者（预览版）](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview)：用来授予对 Blob 存储资源的读取/写入/删除权限。
- [存储 Blob 数据读者（预览版）](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader-preview)：用来授予对 Blob 存储资源的只读权限。
- [存储队列数据参与者（预览版）](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor-preview)：用来授予对 Azure 队列的读取/写入/删除权限。
- [存储队列数据读者（预览版）](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader-preview)：用来授予对 Azure 队列的只读权限。

有关如何为 Azure 存储定义内置角色的详细信息，请参阅[了解角色定义](../articles/role-based-access-control/role-definitions.md#management-and-data-operations-preview)。 有关创建自定义 RBAC 角色的信息，请参阅[创建用于 azure 基于角色的访问控制的自定义角色](../articles/role-based-access-control/custom-roles.md)。 
