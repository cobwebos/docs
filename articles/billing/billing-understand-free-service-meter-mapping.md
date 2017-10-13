---
title: "免费帐户的服务测定仪映射 — Azure | Microsoft Docs"
description: "了解免费帐户包含的服务的服务测定仪映射。"
services: 
documentationcenter: 
author: amberbhargava
manager: amberb
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: amberb
ms.openlocfilehash: 9d7e355e755f2bac8929ab16f7f71aa3b0702658
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="understand-free-service-to-meter-mapping"></a>了解免费服务测定仪映射

各 Azure 服务根据测定仪发出使用率，Azure 计费系统据此向用户收取服务费用。 为了更好地了解免费服务的使用率，先来看看这些服务的服务测定仪映射。 若要了解如何创建免费服务，请参阅[创建 Azure 免费帐户包含的免费服务](billing-create-free-services-included-free-account.md)。

## <a name="service-to-meter-mapping-for-free-account-eligible-services"></a>免费帐户中符合条件的服务的服务测定仪映射 

|    服务   | Azure 门户上的测定仪名称 | 使用率文件/API 中的测定仪名称 | 测定仪 ID |
| ------------ | -------------------------- | -------------------------| -------- |
| B1S Linux VM | 计算小时数 – Standard_B1 VM | 计算小时数 – 免费 | 8260cba2-4437-47d1-a31e-2561cd370f50
| B1S Windows VM | 计算小时数 – Standard_B1 VM (Windows) | 计算小时数 – 免费 | ff3e6fa5-ee46-478e-8d0e-b629f4f8a8ac
| B1S VM – 公共 IP 地址  | IP 地址小时数 – 公共 IP 地址 | IP 地址小时数 – 免费 | ae56b367-2708-4454-a3d9-2be7b2364ea1
| CosmosDB | 存储 (GB) – Cosmos DB | 存储 (GB) – 免费 | 59c78b09-08e2-466a-9f3b-57a94c9e2f31
| CosmosDB | 100 个请求单位（小时数）– Cosmos DB | 100 个请求单位（小时数）– 免费 | 5d638a6f-e221-41cf-ae3f-0f81d368cef6 
| 文件存储 | 标准 IO – 文件 (GB) – 本地冗余 | 标准 IO – 文件 (GB) – 免费 | a7f2aa67-b9a2-4593-a413-6ec86d6c8e5b
| 文件存储 | 标准 IO – 文件读取操作单元（以 10,000 计） | 标准 IO – 文件读取操作单元（以 10,000 计）– 免费 | 6207404d-3389-4d20-9087-cc078ddc3fd9
| 文件存储 | 标准 IO – 文件写入操作单元（以 10,000 计） | 标准 IO – 文件写入操作单元（以 10,000 计）– 免费 | 223d8004-d29a-46cf-b4f4-d2d34b12548b
| 文件存储 | 标准 IO – 文件协议操作单元（以 10,000 计） | 标准 IO – 文件协议操作单元（以 10,000 计）– 免费 | a347d8cc-51d1-4a0e-b9eb-76f67566c3f5
| 文件存储 | 标准 IO – 文件列表操作单元（以 10,000 计） | 标准 IO – 文件列表操作单元（以 10,000 计）– 免费 | e8ae79ad-c2ab-4d82-b226-dd3c33dfd40c
| 热块 Blob 存储 | 标准 IO – 热块 Blob 读取操作（以 10,000 计） | 标准 IO – 热块 Blob 读取操作（以 10,000 计）– 免费 |fd7cfa1e-026e-4be1-871b-1c2386e8902e
| 热块 Blob 存储 | 标准 IO – 热块 Blob (GB) – 本地冗余 | 标准 IO – 热块 Blob (GB) – 免费 | 67a3a3fd-826f-42c1-8843-bffa14f0da13
| 热块 Blob 存储 | 标准 IO – 热块 Blob 写入操作（以 10,000 计） | 标准 IO – 热块 Blob 写入操作（以 10,000 计）– 免费 | b34bbb76-edce-4c2d-a288-81a2db1fea53
| 热块 Blob 存储  | 标准 IO – 热块 Blob 写入/列出操作（以 10,000 计） | 标准 IO – 热块 Blob 写入/列出操作（以 10,000 计）– 免费 | 7e68cf36-1198-4d3b-baa7-86a74c5b3079
| 托管磁盘 *  | 标准托管磁盘/快照 (GB) – 本地冗余 | 标准托管磁盘/快照 (GB) – 免费 | ad94c237-52a5-4804-ae65-38c5bf85ef42
| 托管磁盘 *  | 标准托管磁盘操作数（以 10,000 计） | 标准托管磁盘操作数（以 10,000 计）– 免费 | 82cc6ea4-0abd-43ac-acc0-ec34edf0f14c
| 托管磁盘 *  | 高级存储 – 页 Blob/P6（单元数）– 本地冗余 | 高级存储 – 页 Blob/P6（单元数）– 免费 | 2b98c168-27ca-4cc1-b509-e887dec87657
| SQL 数据库 | 标准 S0 数据库天数 – SQL 数据库 | 标准 S0 数据库天数 – 免费 | dd6b69d3-9be0-4a91-abff-2c58bbcafd1d
| 共享 – 带宽 ** | 数据发送 (GB) | 数据发送 (GB) – 免费 | 0fc067a1-65d2-46da-b24b-7a9cbe2c69bd

\* 如果创建 Windows 虚拟机，并选择托管磁盘，则将使用托管磁盘测定仪作为虚拟机的一部分。

\** 可以通过多个服务使用共享测定仪。 例如，虚拟机和存储都根据数据发送 (GB) 测定仪发出使用率。





## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果需要帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。
