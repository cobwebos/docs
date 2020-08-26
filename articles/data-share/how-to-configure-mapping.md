---
title: 在 Azure 数据共享中配置数据集映射
description: 了解如何使用 Azure 数据共享为接收的共享配置数据集映射。
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 08/14/2020
ms.openlocfilehash: eed3e8275400a3e677df53b9d62cf0e0bc70271c
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2020
ms.locfileid: "88257836"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>如何为 Azure 数据共享中接收的共享配置数据集映射

本文介绍如何使用 Azure 数据共享为接收的共享配置数据集映射。 你可能需要配置数据集映射，以指定要在其中接收数据的目标数据存储，或者需要更改目标数据存储。

## <a name="navigate-to-a-received-data-share"></a>导航到收到的数据共享

在 Azure 数据共享服务中，导航到接收到的共享，然后选择 " **数据集** " 选项卡。 

![数据集映射](./media/dataset-mapping.png "数据集映射") 

选中要为其分配目标的数据集旁边的复选框。 选择 " **+ 映射到目标** " 以选择新的目标存储。 如果已映射数据集，并且要更改目标数据存储，请选择 "首先 **取消映射"** 。

![映射到目标](./media/dataset-map-target.png "映射到目标") 

## <a name="select-a-target-store"></a>选择目标存储

选择要将数据置于居住的目标数据存储类型。 对于基于快照的共享，任何之前映射的存储帐户中已存在的任何数据都不会自动移动到新的目标存储中。 对于 "就地共享"，请选择指定位置中的数据存储。 此位置是数据访问接口的源数据存储所在的 Azure 数据中心。

![目标存储帐户](./media/dataset-map-target-sql.png "目标存储") 

## <a name="select-a-file-format-sql-sources-only"></a>选择一种文件格式 (仅限 SQL 源) 

如果源数据来自基于 SQL 的源，并且您希望将其作为文件接收，则可以选择在其中接收的格式。 

![选择格式](./media/sql-file-formats.png "SQL 文件格式")

## <a name="next-steps"></a>后续步骤

若要了解如何开始共享数据，请继续阅读[共享数据](share-your-data.md)教程。



