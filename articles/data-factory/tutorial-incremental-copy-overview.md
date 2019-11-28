---
title: '使用 Azure 数据工厂以增量方式复制数据 '
description: 这些教程介绍如何以增量方式将数据从源数据存储复制到目标数存储。 第一个从一个表复制数据。
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: yexu
ms.openlocfilehash: 42dcd6ecc6df1c9877581d89bf22724054e305d0
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74217272"
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>以增量方式将数据从源数据存储加载到目标数据存储

在数据集成解决方案中，一种广泛使用的方案是在完成初始的完整数据加载后以增量方式加载数据。 此部分的教程介绍如何使用不同的方法，通过 Azure 数据工厂以增量方式加载数据。

## <a name="delta-data-loading-from-database-by-using-a-watermark"></a>使用水印从数据库进行增量数据加载
在这种情况下，请在源数据库中定义水印。 水印是一个列，其中包含上次更新的时间戳或增量键。 增量加载解决方案加载旧水印和新水印之间的已更改数据。 此方法的工作流见下图： 

![水印使用工作流](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

有关分步说明，请参阅以下教程： 
- [以增量方式将 Azure SQL 数据库中某个表的数据复制到 Azure Blob 存储](tutorial-incremental-copy-powershell.md)
- [以增量方式将数据从本地 SQL Server 中的多个表复制到 Azure SQL 数据库](tutorial-incremental-copy-multiple-tables-powershell.md)

对于模板，请参阅以下内容：
- [使用控制表进行增量复制](solution-template-delta-copy-with-control-table.md)

## <a name="delta-data-loading-from-sql-db-by-using-the-change-tracking-technology"></a>使用更改跟踪技术进行的增量数据加载
在 SQL Server 和 Azure SQL 数据库中，更改跟踪技术是一种轻型解决方案，为应用程序提供有效的更改跟踪机制。 应用程序可以使用这种技术轻松地确定插入、更新或删除的数据。 

此方法的工作流见下图：

![更改跟踪使用工作流](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

有关分步说明，请参阅以下教程： <br/>
- [使用更改跟踪技术，以增量方式将 Azure SQL 数据库中的数据复制到 Azure Blob 存储](tutorial-incremental-copy-change-tracking-feature-powershell.md)

## <a name="loading-new-and-changed-files-only-by-using-lastmodifieddate"></a>仅使用 LastModifiedDate 加载新文件和已更改文件
只能使用 LastModifiedDate 将新文件和已更改文件复制到目标存储。 ADF 会扫描来自源存储的所有文件，按其 LastModifiedDate 应用文件筛选器，然后仅将自上次以来的新文件和已更新文件复制到目标存储。  请注意，如果让 ADF 扫描大量文件，但是仅将少量文件复制到目标，则仍然会预计由于文件扫描所导致的较长持续时间也十分耗时。   

有关分步说明，请参阅以下教程： <br/>
- [基于 LastModifiedDate 以增量方式将新文件和已更改文件从 Azure Blob 存储复制到 Azure Blob 存储](tutorial-incremental-copy-lastmodified-copy-data-tool.md)

对于模板，请参阅以下内容：
- [按 LastModifiedDate 复制新文件](solution-template-copy-new-files-lastmodifieddate.md)

## <a name="loading-new-files-only-by-using-time-partitioned-folder-or-file-name"></a>仅通过使用时间分区文件夹或文件名称来加载新文件。
仅可以复制新文件，其中文件或文件夹已经过时间分区，时间片信息作为文件或文件夹名称的一部分（如 /yyyy/mm/dd/file.csv）。 这是用于增量加载新文件的性能最好的方法。 

有关分步说明，请参阅以下教程： <br/>
- [基于时间分区文件夹或文件名称以增量方式将新文件从 Azure Blob 存储复制到 Azure Blob 存储](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)

## <a name="next-steps"></a>后续步骤
转到以下教程： 

> [!div class="nextstepaction"]
>[以增量方式将 Azure SQL 数据库中某个表的数据复制到 Azure Blob 存储](tutorial-incremental-copy-powershell.md)
