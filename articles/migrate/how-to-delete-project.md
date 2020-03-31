---
title: 删除 Azure Migrate 项目
description: 介绍如何创建 Azure 迁移项目并添加评估/迁移工具。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/22/2019
ms.author: raynew
ms.openlocfilehash: 55842d36cddb2a7851ff5bd7002c20e9873158f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73512725"
---
# <a name="delete-an-azure-migrate-project"></a>删除 Azure Migrate 项目

本文介绍如何删除 Azure[迁移](migrate-overview.md)项目。


## <a name="before-you-start"></a>开始之前

在删除项目之前：

- 删除项目时，将删除项目和发现的计算机元数据。
- 如果将日志分析工作区附加到服务器评估工具以进行分析，请决定是否要删除工作区。 
    - 不会自动删除工作区。 手动删除它。
    - 在删除工作区之前，请验证工作区的用途。 同一日志分析工作区可用于多个方案。
    - 在删除项目之前，可以在**OMS 工作区**下在**Azure 迁移 - 服务器** > **Azure 迁移 - 服务器评估**中找到指向工作区的链接。
    - 要删除项目后删除工作区，请查找相关资源组中的工作区，然后按照[这些说明操作](../azure-monitor/platform/delete-workspace.md)。


## <a name="delete-a-project"></a>删除项目


1. 在 Azure 门户中，打开在其中创建项目的资源组。
2. 在资源组页中，选择 **"显示隐藏类型**"。
3. 选择要删除的项目和相关资源。
    - Azure 迁移项目的资源类型是**Microsoft.迁移/迁移项目**。
    - 在下一节中，查看为 Azure 迁移项目中的发现、评估和迁移而创建的资源。
    - 如果资源组仅包含 Azure 迁移项目，则可以删除整个资源组。
    - 如果要从以前的 Azure 迁移版本中删除项目，则步骤相同。 这些项目的资源类型是**迁移项目**。


## <a name="created-resources"></a>已创建资源

这些表汇总了为 Azure 迁移项目中的发现、评估和迁移而创建的资源。

> [!NOTE]
> 请谨慎删除密钥保管库，因为它可能包含安全密钥。

### <a name="vmwarephysical-server"></a>VMware/物理服务器

**资源** | **类型**
--- | ---
"设备名称"kv | 密钥保管库
"设备名称"站点 | 微软.关闭Azure/VMwareSites
"项目名称" | 微软.迁移/迁移项目
"项目名称"项目 | 微软.迁移/评估项目
"项目名称"rsvault | 恢复服务保管库
"项目名称"-迁移库-* | 恢复服务保管库
迁移 appligwsa* | 存储帐户
迁移 applilsa* | 存储帐户
迁移应用* | 存储帐户
迁移 applikv* | 密钥保管库
迁移applisbns16041 | 服务总线命名空间

### <a name="hyper-v-vm"></a>Hyper-V VM 

**资源** | **类型**
--- | ---
"项目名称" | 微软.迁移/迁移项目
"项目名称"项目 | 微软.迁移/评估项目
HyperV_kv | 密钥保管库
HyperV_Site | 微软.关闭Azure/超网站
"项目名称"-迁移库-* | 恢复服务保管库


## <a name="next-steps"></a>后续步骤

了解如何添加其他[评估和](how-to-assess.md)[迁移](how-to-migrate.md)工具。 
