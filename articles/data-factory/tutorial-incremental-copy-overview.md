---
title: "使用 Azure 数据工厂以增量方式复制数据 | Microsoft Docs"
description: "这些教程介绍如何以增量方式将数据从源数据存储复制到目标数存储。 第一个从一个表复制数据。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/05/2017
ms.author: shlo
ms.openlocfilehash: f23ca6862d0a0e67245f02dc723f61da8f41b6a0
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2017
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>以增量方式将数据从源数据存储加载到目标数据存储

在数据集成解决方案中，一种广泛使用的方案是在完成初始的完整数据加载后以增量方式加载数据。 此部分的教程介绍如何使用不同的方法，通过 Azure 数据工厂第 2 版以增量方式加载数据。

## <a name="delta-data-loading-by-using-a-watermark"></a>使用水印进行增量数据加载
在这种情况下，请在源数据库中定义水印。 水印是一个列，其中包含上次更新的时间戳或增量键。 增量加载解决方案加载旧水印和新水印之间的已更改数据。 此方法的工作流见下图： 

![水印使用工作流](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

有关分步说明，请参阅以下教程： 

- [以增量方式将 Azure SQL 数据库中某个表的数据复制到 Azure Blob 存储](tutorial-incremental-copy-powershell.md)
- [以增量方式将数据从本地 SQL Server 中的多个表复制到 Azure SQL 数据库](tutorial-incremental-copy-multiple-tables-powershell.md)


## <a name="delta-data-loading-by-using-the-change-tracking-technology"></a>使用更改跟踪技术进行的增量数据加载
在 SQL Server 和 Azure SQL 数据库中，更改跟踪技术是一种轻型解决方案，为应用程序提供有效的更改跟踪机制。 应用程序可以使用这种技术轻松地确定插入、更新或删除的数据。 

此方法的工作流见下图：

![更改跟踪使用工作流](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

有关分步说明，请参阅以下教程： <br/>
[使用更改跟踪技术，以增量方式将 Azure SQL 数据库中的数据复制到 Azure Blob 存储](tutorial-incremental-copy-change-tracking-feature-powershell.md)


## <a name="next-steps"></a>后续步骤
转到以下教程： 

> [!div class="nextstepaction"]
>[以增量方式将 Azure SQL 数据库中某个表的数据复制到 Azure Blob 存储](tutorial-incremental-copy-powershell.md)
