---
title: include 文件
description: include 文件
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 11/06/2019
ms.openlocfilehash: 754c9799ed4c2fd90cbcf1e9717b0be21edd54cb
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2020
ms.locfileid: "75659860"
---
>[!IMPORTANT]
>可以使用你创建的、用作其他 Azure 机器学习教程和操作指南文章的先决条件的资源。

### <a name="delete-everything"></a>删除所有内容

如果你不打算使用所创建的任何内容，请删除整个资源组，以免产生任何费用。

1. 在 Azure 门户的窗口左侧选择“资源组”  。
 
   ![在 Azure 门户中删除资源组](./media/aml-ui-cleanup/delete-resources.png)

1. 在列表中选择你创建的资源组。

1. 选择“删除资源组”  。

删除该资源组也会删除在设计器中创建的所有资源。 

### <a name="delete-individual-assets"></a>删除各项资产

在创建试验的设计器中删除各个资产，方法是将其选中，然后选择“删除”按钮。 

此处创建的计算目标在未使用时，会自动缩减到零个节点。  此操作旨在最大程度地减少费用。 若要删除计算目标，请执行以下步骤：

![删除资产](./media/aml-ui-cleanup/delete-asset.png)

可以通过选择每个数据集并选择“注销”  ，从工作区中注销数据集。

![取消注册数据集](./media/aml-ui-cleanup/unregister-dataset1225.png)

若要删除数据集，请使用 Azure 门户或 Azure 存储资源管理器访问存储帐户，然后手动删除这些资产。


