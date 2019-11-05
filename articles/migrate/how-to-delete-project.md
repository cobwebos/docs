---
title: 删除 Azure Migrate 项目
description: 介绍如何创建 Azure Migrate 项目并添加评估/迁移工具。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/22/2019
ms.author: raynew
ms.openlocfilehash: 55842d36cddb2a7851ff5bd7002c20e9873158f5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512725"
---
# <a name="delete-an-azure-migrate-project"></a>删除 Azure Migrate 项目

本文介绍如何删除[Azure Migrate](migrate-overview.md)项目。


## <a name="before-you-start"></a>开始之前

删除项目之前：

- 删除项目时，项目和发现的计算机元数据将被删除。
- 如果已将 Log Analytics 工作区附加到服务器评估工具进行依赖关系分析，请决定是否要删除工作区。 
    - 不会自动删除此工作区。 请手动将其删除。
    - 在删除工作区之前，验证工作区的用途。 同一 Log Analytics 工作区可用于多个方案。
    - 在删除项目之前，你可以在 " **OMS 工作区**" 下的 " **Azure Migrate** server > **Azure Migrate**" 中找到工作区的链接。
    - 若要在删除项目后删除工作区，请在相关资源组中找到工作区，并按照[这些说明](../azure-monitor/platform/delete-workspace.md)进行操作。


## <a name="delete-a-project"></a>删除项目


1. 在 Azure 门户中，打开在其中创建项目的资源组。
2. 在 "资源组" 页上，选择 "**显示隐藏的类型**"。
3. 选择要删除的项目和关联的资源。
    - Azure Migrate 项目的资源类型为 migrateprojects **/** 。
    - 在下一部分中，查看在 Azure Migrate 项目中创建的用于发现、评估和迁移的资源。
    - 如果资源组仅包含 Azure Migrate 项目，则可以删除整个资源组。
    - 如果要从 Azure Migrate 的以前版本中删除项目，则步骤是相同的。 这些项目的资源类型为 "**迁移项目**"。


## <a name="created-resources"></a>创建的资源

这些表汇总了为 Azure Migrate 项目中的发现、评估和迁移创建的资源。

> [!NOTE]
> 请注意删除密钥保管库，因为它可能包含安全密钥。

### <a name="vmwarephysical-server"></a>VMware/物理服务器

**资源** | **类型**
--- | ---
"Appliancename" kv | 密钥保管库
"Appliancename" 站点 | OffAzure/VMwareSites
ProjectName | Microsoft. 迁移/migrateprojects
"项目名称" 项目 | Microsoft. 迁移/assessmentProjects
"项目名称" rsvault | 恢复服务保管库
"项目名称"-MigrateVault-* | 恢复服务保管库
migrateappligwsa* | 存储帐户
migrateapplilsa* | 存储帐户
migrateapplicsa* | 存储帐户
migrateapplikv* | 密钥保管库
migrateapplisbns16041 | 服务总线命名空间

### <a name="hyper-v-vm"></a>Hyper-V VM 

**资源** | **类型**
--- | ---
ProjectName | Microsoft. 迁移/migrateprojects
"项目名称" 项目 | Microsoft. 迁移/assessmentProjects
HyperV * kv | 密钥保管库
HyperV * 站点 | OffAzure/HyperVSites
"项目名称"-MigrateVault-* | 恢复服务保管库


## <a name="next-steps"></a>后续步骤

了解如何添加其他[评估](how-to-assess.md)和[迁移](how-to-migrate.md)工具。 
