---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/23/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d4f57eca89cbb68d61546c6d5ce5bcd04f9256e7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799501"
---
Azure 存储提供多种类型的存储帐户。 每个类型支持不同的功能，并具有自身的定价模型。 在创建存储帐户之前，请考虑到这些差异，以确定最适合应用程序的帐户类型。 存储帐户的类型包括：

- **常规用途 v2 帐户**：Blob、文件、队列和表的基本存储帐户类型。 建议在大多数情况下使用 Azure 存储。
- **常规用途 v1 帐户**：Blob、文件、队列和表的旧帐户类型。 如果可能，请改用常规用途 v2 帐户。
- **块 blob 存储帐户**:使用高级性能特征的仅限 blob 的存储帐户。 建议对具有高事务率，使用较小的对象，也无需进行一致地低存储延迟方案。
- **文件存储 （预览版） 存储帐户**:使用高级性能特征的仅限文件的存储帐户。 建议为企业或高性能缩放应用程序。
- **Blob 存储帐户**：仅限 Blob 的存储帐户。 如果可能，请改用常规用途 v2 帐户。

下表描述了存储帐户的类型及其功能：

| 存储帐户类型 | 支持的服务                       | 支持的性能层      | 支持的访问层         | 复制选项               | 部署模型<sup>1</sup> | 加密<sup>2</sup> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| 常规用途 V2   | Blob、文件、队列、表和磁盘       | Standard、 Premium<sup>5</sup> | 热、冷、存档<sup>3</sup> | LRS、ZRS<sup>4</sup>、GRS、RA-GRS | 资源管理器             | 加密              |
| 常规用途 V1   | Blob、文件、队列、表和磁盘       | Standard、 Premium<sup>5</sup> | 不适用                            | LRS、GRS、RA-GRS                  | 资源管理器、经典    | 加密              |
| 块 blob 存储   | Blob（仅限块 Blob 和追加 Blob） | 高级                       | 不适用                            | LRS                               | 资源管理器             | 加密              |
| 文件存储 （预览版）   | 仅限文件 | 高级                       | 不适用                            | LRS                               | 资源管理器             | 加密              |
| Blob 存储         | Blob（仅限块 Blob 和追加 Blob） | 标准                      | 热、冷、存档<sup>3</sup> | LRS、GRS、RA-GRS                  | 资源管理器             | 加密              |

<sup>1</sup>建议使用 Azure 资源管理器部署模型。 仍将在某些位置创建使用经典部署模型的存储帐户，继续支持现有的经典帐户。 有关详细信息，请参阅 [Azure 资源管理器与经典部署：了解部署模型和资源状态](../articles/azure-resource-manager/resource-manager-deployment-model.md)。

<sup>2</sup>使用针对静态数据的存储服务加密 (SSE) 来加密所有存储帐户。 有关详细信息，请参阅[静态数据的 Azure 存储服务加密](../articles/storage/common/storage-service-encryption.md)。

<sup>3</sup>存档层仅在单个 Blob 级别可用，在存储帐户级别不可用。 只能存档块 Blob 和追加 Blob。 有关详细信息，请参阅 [Azure Blob 存储：热、冷、存档存储层](../articles/storage/blobs/storage-blob-storage-tiers.md)。

<sup>4</sup>区域冗余存储 (ZRS) 仅适用于标准常规用途 v2 存储帐户。 有关 ZRS 的详细信息，请参阅[区域冗余存储 (ZRS)：高度可用的 Azure 存储应用程序](../articles/storage/common/storage-redundancy-zrs.md)。 有关其他复制选项的详细信息，请参阅 [Azure 存储复制](../articles/storage/common/storage-redundancy.md)。

<sup>5</sup>高级性能的常规用途 v2 和常规用途 v1 帐户是可用于磁盘和页 blob。