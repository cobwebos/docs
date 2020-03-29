---
title: 在 Azure 数据共享中配置数据集映射
description: 了解如何使用 Azure 数据共享为收到的共享配置数据集映射。
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 1ff432c2073536448c8194ebe537c8bf8cf00663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964237"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>如何为 Azure 数据共享中接收的共享配置数据集映射

本文介绍如何使用 Azure 数据共享为接收共享配置数据集映射。 如果您接受了数据共享邀请，但选择"稍后接受和配置"，或者数据就地共享，则需要执行此操作。 如果需要更改要与您共享的数据的目标，或者要将数据接收到 SQL Server 中，则可能需要配置数据集映射。 

## <a name="navigate-to-a-received-data-share"></a>导航到接收的数据共享

在 Azure 数据共享服务中，导航到收到的共享并选择"**详细信息**"选项卡。 

![数据集映射](./media/dataset-mapping.png "数据集映射") 

选中要将目标分配给的数据集旁边的复选框。 选择 **"取消映射**"以取消映射现有地图。 选择 **" 地图"以选择要定位**的目标。 

![映射到目标](./media/dataset-map-target.png "映射到目标") 

## <a name="select-a-new-target-store"></a>选择新的目标存储

选择要将数据输入的目标数据类型。 对于基于快照的共享，任何以前映射的存储帐户中已存在的任何数据都不会自动移动到新的目标存储。 对于就地共享，请选择指定位置中的数据存储。 位置是数据提供程序的源数据存储所在的 Azure 数据中心。

![目标存储帐户](./media/dataset-map-target-sql.png "目标存储") 

## <a name="select-a-file-format-sql-sources-only"></a>选择文件格式（仅限 SQL 源）

如果源数据来自基于 SQL 的源，则可以选择接收该数据源的格式。 

![选择格式](./media/sql-file-formats.png "SQL 文件格式")

## <a name="next-steps"></a>后续步骤

若要了解如何开始共享数据，请继续阅读[共享数据](share-your-data.md)教程。



