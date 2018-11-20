---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/18/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3b770c3e51b17fa5d70abb03360e17e1073974d8
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2018
ms.locfileid: "51222220"
---
若要在 Azure 门户中创建常规用途 v2 存储帐户，请执行以下步骤：

1. 在 Azure 门户中，选择“所有服务”。 在资源列表中，键入“存储帐户”。 开始键入时，会根据输入筛选该列表。 选择“存储帐户”。
1. 在显示的“存储帐户”窗口中，选择“添加”。
1. 选择要在其中创建存储帐户的订阅。
1. 在“资源组”字段下，选择“新建”。 输入新资源组的名称，如下图中所示。

    ![显示如何在门户中创建资源组的屏幕截图](./media/storage-create-account-portal-include/create-resource-group.png)

1. 然后，输入存储帐户的名称。 所选名称在 Azure 中必须唯一。 该名称还必须为 3 到 24 个字符，并且只能包含数字和小写字母。
1. 选择存储帐户的位置或使用默认位置。
1. 将这些字段设置为其默认值：

   |字段  |值  |
   |---------|---------|
   |部署模型     |资源管理器         |
   |性能     |标准         |
   |帐户类型     |StorageV2（常规用途 v2）         |
   |复制     |本地冗余存储 (LRS)         |
   |访问层     |热         |

1. 选择“查看+创建”可查看存储帐户设置并创建帐户。
1. 选择“创建”。

有关存储帐户类型和其他存储帐户设置的详细信息，请参阅 [Azure 存储帐户概述](https://docs.microsoft.com/azure/storage/common/storage-account-overview)。 有关资源组的详细信息，请参阅 [Azure 资源管理器概述](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。 
