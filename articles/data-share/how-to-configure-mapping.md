---
title: 在 Azure 数据共享预览中配置数据集映射
description: 了解如何使用 Azure 数据共享预览为接收的共享配置数据集映射。
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 035235116240bdc6de3bc689c2430fee018b202d
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169136"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share-preview"></a>如何在 Azure 数据共享预览中为接收的共享配置数据集映射

本文介绍如何使用 Azure 数据共享预览为接收的共享配置数据集映射。 如果接受了数据共享邀请，但选择了 "以后接受并配置"，则需要执行此操作。 否则，你可能只是想要更改接收的数据的目标存储帐户。 

## <a name="navigate-to-a-received-data-share"></a>导航到收到的数据共享

在 Azure 数据共享服务中，导航到接收到的共享，然后选择 "**详细信息**" 选项卡。 

![数据集映射](./media/dataset-mapping.png "数据集映射") 

选中要为其分配目标的数据集旁边的框，然后单击 " **+ 映射到目标**"。 如果已配置了目标存储帐户并且要将映射更改为其他存储帐户，则可能需要先取消映射。 

![映射]到目标(./media/dataset-map-target.png "映射到目标") 

## <a name="select-a-new-storage-account"></a>选择新的存储帐户 

选择要将数据存储到的存储帐户。 请注意，任何之前映射的存储帐户中已存在的数据将不会自动移动到新的存储帐户。

![目标存储帐户](./media/map-target.png "目标存储") 

## <a name="next-steps"></a>后续步骤

若要了解如何开始共享数据，请继续阅读[共享数据](share-your-data.md)教程。



