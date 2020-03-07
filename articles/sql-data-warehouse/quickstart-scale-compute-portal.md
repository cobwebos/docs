---
title: '快速入门：缩放计算 - Azure 门户 '
description: 在 Azure 门户中缩放 SQL 池中的计算。 横向扩展计算为提高性能或缩放重新计算以节约成本。
services: sql-data-warehouse
author: Antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: implement
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 7463849223fdf81466237c7d0c912763988e80e6
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78200325"
---
# <a name="quickstart-scale-compute-in-azure-synapse-analytics-sql-pool-in-the-azure-portal"></a>快速入门：在 Azure 门户中缩放 Azure Synapse Analytics SQL 池中的计算

在 Azure 门户中缩放 SQL 池中的计算。 [横向扩展计算](sql-data-warehouse-manage-compute-overview.md)以提高性能或按比例缩减计算以节约成本。 

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com/)。

## <a name="before-you-begin"></a>开始之前

可以缩放现有的 SQL 池，或使用[快速入门：创建并连接 - 门户](create-data-warehouse-portal.md)创建名为“mySampleDataWarehouse”的 SQL 池  。  本快速入门教程缩放 **mySampleDataWarehouse**。

>[!Note]
>SQL 池必须处于联机状态才能进行缩放。 

## <a name="scale-compute"></a>缩放计算

可以通过增加或减少数据仓库单位来缩放 SQL 池计算资源。 [创建和连接 - 门户] 快速入门(create-data-warehouse-portal.md)创建了 **mySampleDataWarehouse** 并使用 400 DWU 对其进行了初始化。 以下步骤调整为 DWU **mySampleDataWarehouse**。

更改数据仓库单位：

1. 单击 Azure 门户左侧页中的“Azure Synapse Analytics (以前称为 SQL DW)”  。
2. 从“Azure Synapse Analytics (以前称为 SQL DW)”  页中选择 **mySampleDataWarehouse**。 此时将打开 SQL 池。
3. 单击“缩放”  。

    ![单击“缩放”](media/quickstart-scale-compute-portal/click-scale.png)

2. 在“缩放”窗格中，向左或向右移动滑块，以更改 DWU 设置。 然后选择“缩放”。

    ![移动滑块](media/quickstart-scale-compute-portal/scale-dwu.png)

## <a name="next-steps"></a>后续步骤
现已了解如何缩放 SQL 池的计算。 若要详细了解 SQL 池，请继续阅读有关加载数据的教程。

> [!div class="nextstepaction"]
>[将数据加载到 SQL 池中](load-data-from-azure-blob-storage-using-polybase.md)
