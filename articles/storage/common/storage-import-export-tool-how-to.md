---
title: 使用 Azure 导入/导出工具 | Microsoft Docs
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
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: 20a720833842f9579fd4fccaa39e964def48197e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
ms.locfileid: "23059332"
---
# <a name="using-the-azure-importexport-tool"></a>使用 Azure 导入/导出工具 

使用 Azure 导入/导出工具 (WAImportExport.exe) 可以创建和管理 Azure 导入/导出服务的作业，将大量数据传入或传出 Azure Blob 存储。

本文档适用于最新版本的 Azure 导入/导出工具。 有关经典部署模型的使用信息，请参阅[使用 Azure 导入/导出工具 v1](storage-import-export-tool-how-to-v1.md)。

以下文章介绍如何：  

- 安装和设置 Azure 导入/导出工具。
- 为作业准备硬盘驱动器，以便将数据从驱动器导入 Azure Blob 存储。
- 使用复制日志文件查看作业状态。 
- 修复导入作业。 
- 修复导出作业。 
- 排查 Azure 导入/导出工具问题。 

## <a name="next-steps"></a>后续步骤

* [设置 WAImportExport 工具](storage-import-export-tool-setup.md)