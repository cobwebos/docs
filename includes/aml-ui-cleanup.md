---
title: include 文件
description: include 文件
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 10/16/2019
ms.openlocfilehash: 0071b0df2c2e173eced1722372f88b1de2708afa
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692361"
---
>[!IMPORTANT]
>可以使用你创建的、用作其他 Azure 机器学习服务教程和操作指南文章的先决条件的资源。

### <a name="delete-everything"></a>删除所有内容

如果你不打算使用所创建的任何内容，请删除整个资源组，以免产生任何费用：

1. 在 Azure 门户的窗口左侧选择“资源组”  。
 
   ![在 Azure 门户中删除资源组](./media/aml-ui-cleanup/delete-resources.png)

1. 在列表中选择你创建的资源组。

1. 在窗口的右侧，选择省略号按钮 ( **...** )。

1. 选择“删除资源组”  。

删除该资源组也会删除在可视界面中创建的所有资源。  

### <a name="delete-only-the-compute-target"></a>仅删除计算目标

此处创建的计算目标在未使用时，会自动缩减到零个节点。  这样可以最大限定地减少费用。 若要删除计算目标，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中打开你的工作区。

    ![删除计算目标](./media/aml-ui-cleanup/delete-compute-target.png)

1. 在工作区的“计算”部分选择资源。 

1. 选择“删除”。 

### <a name="delete-individual-assets"></a>删除各项资产

在创建试验的可视界面中删除各个资产，方法是将其选中，然后选择“删除”按钮。  可以通过选择每个数据集并选择“注销”  ，从工作区中注销数据集。

![删除资产](./media/aml-ui-cleanup/delete-asset.png)
