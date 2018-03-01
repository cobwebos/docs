---
title: "快速入门：在 Azure SQL 数据仓库中横向扩展计算资源 - Azure 门户 | Microsoft Docs"
description: "用于管理计算能力的 Azure 门户任务。 通过调整 DWU 缩放计算资源。 或者，暂停和恢复计算资源来节省成本。"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 01/31/2018
ms.author: elbutter;barbkess
ms.openlocfilehash: 6b86042ed6b95ba49fa2089ba36b1dbe9a61cc40
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-the-azure-portal"></a>快速入门：使用 Azure 门户在 Azure SQL 数据仓库中缩放计算资源

使用 Azure 门户在 Azure SQL 数据仓库中缩放计算资源。 [横向扩展计算](sql-data-warehouse-manage-compute-overview.md)（可提高性能）或缩放重新计算（可节约成本）。 

如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="before-you-begin"></a>开始之前

可以缩放现有的数据仓库，或使用[创建并连接 - 门户](create-data-warehouse-portal.md)创建名为“mySampleDataWarehouse”的数据仓库。  本快速入门教程缩放 **mySampleDataWarehouse**。

## <a name="scale-compute"></a>缩放计算

在 SQL 数据仓库中，可以通过调整数据仓库单位来增加或减少计算资源。 [创建和 Connect - 门户](create-data-warehouse-portal.md)创建 **mySampleDataWarehouse** 并初始化 400 DWU。 以下步骤调整为 DWU **mySampleDataWarehouse**。

更改数据仓库单位：

1. 在 Azure 门户的左侧页面中，单击“SQL 数据库”。
2. 从“SQL 数据库”页中选择“mySampleDataWarehouse”。 此操作打开数据仓库。
3. 单击“缩放”。

    ![单击“缩放”](media/quickstart-scale-compute-portal/click-scale.png)

2. 在“缩放”窗格中，向左或向右移动滑块，以更改 DWU 设置。

    ![移动滑块](media/quickstart-scale-compute-portal/scale-dwu.png)

3. 单击“保存”。 此时会显示确认消息。 单击“是”以确认或“否”以取消。

    ![点击“保存”(Save)](media/quickstart-scale-compute-portal/confirm-change.png)



## <a name="next-steps"></a>后续步骤
现在已暂停并恢复了数据仓库的计算。 若要了解有关 Azure SQL 数据仓库的详细信息，请继续有关加载数据的教程。

> [!div class="nextstepaction"]
>[将数据加载到 SQL 数据仓库](load-data-from-azure-blob-storage-using-polybase.md)
