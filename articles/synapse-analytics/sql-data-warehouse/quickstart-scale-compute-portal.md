---
title: 缩放 Synapse SQL 池的计算（Azure 门户）
description: 可以使用 Azure 门户缩放 Synapse SQL 池（数据仓库）的计算。
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f92152658b9db83740ffc2de2dc6956003849e06
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350821"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-the-azure-portal"></a>快速入门：使用 Azure 门户缩放 Synapse SQL 池的计算

可以使用 Azure 门户缩放 Synapse SQL 池（数据仓库）的计算。 [横向扩展计算](sql-data-warehouse-manage-compute-overview.md)以提高性能或按比例缩减计算以节约成本。 

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com/)。

## <a name="before-you-begin"></a>开始之前

可以缩放现有的 SQL 池，或使用[快速入门：创建并连接 - 门户](create-data-warehouse-portal.md)创建名为“mySampleDataWarehouse”的 SQL 池  。 本快速入门教程缩放 **mySampleDataWarehouse**。

>[!IMPORTANT] 
>SQL 池必须处于联机状态才能进行缩放。 

## <a name="scale-compute"></a>缩放计算

可以通过增加或减少数据仓库单位来缩放 SQL 池计算资源。 [创建和连接 - 门户] 快速入门(create-data-warehouse-portal.md)创建了 **mySampleDataWarehouse** 并使用 400 DWU 对其进行了初始化。 以下步骤调整为 DWU **mySampleDataWarehouse**。

更改数据仓库单位：

1. 单击 Azure 门户左侧页中的“Azure Synapse Analytics (以前称为 SQL DW)”  。
2. 从“Azure Synapse Analytics (以前称为 SQL DW)”  页中选择 **mySampleDataWarehouse**。 此时将打开 SQL 池。
3. 单击“缩放”  。

    ![单击“缩放”](./media/quickstart-scale-compute-portal/click-scale.png)

2. 在“缩放”窗格中，向左或向右移动滑块，以更改 DWU 设置。 然后选择“缩放”。

    ![移动滑块](./media/quickstart-scale-compute-portal/scale-dwu.png)

## <a name="next-steps"></a>后续步骤
若要详细了解 SQL 池，请继续阅读[将数据加载到 SQL 池](load-data-from-azure-blob-storage-using-polybase.md)教程。 
