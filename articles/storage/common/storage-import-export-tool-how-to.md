---
title: 使用 Azure 导入/导出工具 | Microsoft Docs
description: 了解如何使用导入/导出工具为导入作业准备硬盘驱动器、修复导入作业或导出作业。
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 1d32198b9f15e168899b1dfe482d42d48c7b4277
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526719"
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