---
title: 使用 Azure 导入/导出工具 - v1 | Microsoft Docs
description: 了解如何使用导入/导出工具为导入作业准备硬盘驱动器、修复导入作业或导出作业。
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: f77535bb-d577-438a-bdd3-e15a82e0c543
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/15/2017
ms.author: muralikk
ms.openlocfilehash: 4ce2273cc0dcc456c2edc8c5dd2fc22496f20380
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
ms.locfileid: "23059352"
---
# <a name="using-the-azure-importexport-tool-classic-deployment-model"></a>使用 Azure 导入/导出工具（经典部署模型）

使用 Azure 导入/导出工具 (WAImportExport.exe) 可以创建和管理 Azure 导入/导出服务的作业，将大量数据传入或传出 Azure Blob 存储。

本文档适用于 Azure 导入/导出工具的经典部署模型。 有关该工具的最新版本的使用信息，请参阅[使用 Azure 导入/导出工具](../storage-import-export-tool-how-to.md)。

以下文章介绍如何：

- 安装和设置导入/导出工具。
- 为作业准备硬盘驱动器，以便将数据从驱动器导入 Azure Blob 存储。
- 使用复制日志文件查看作业状态。 
- 修复导入作业。 
- 修复导出作业。 
- 排查在使用 Azure 导入/导出工具的过程中遇到的问题。 

## <a name="next-steps"></a>后续步骤

* [设置 WAImportExport 工具](../storage-import-export-tool-how-to.md)