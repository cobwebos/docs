---
title: "多地域帮助文档 | Microsoft Docs"
description: "了解如何在美国中南部 (SCUS) Azure 区域以外的 Azure 区域中创建工作区并发布 Web 服务。"
services: machine-learning
documentationcenter: 
author: tedway
manager: jhubbard
editor: rmca14
tags: 
ms.assetid: ed0ca8a8-fa53-4e56-b824-2d7e44641967
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/17/2016
ms.author: tedway; neerajkh
translationtype: Human Translation
ms.sourcegitcommit: ec9d29701915d237686625fbc3abec9827fda467
ms.openlocfilehash: 22c30bfea89e3c650d81e1ebae20c9d151e6f36c


---
# <a name="multi-geo-help-documentation"></a>多地域帮助文档
本文介绍如何在不同的 Azure 区域中创建工作区并发布 Web 服务。  [Azure 产品（按区域排列）页](https://azure.microsoft.com/en-us/regions/services/)列出了提供 Azure 机器学习的区域。

## <a name="create-a-workspace"></a>创建工作区
1. 登录到 Azure 经典门户。
2. 依次单击“+ 新建” > “数据服务” > “机器学习” > “快速创建”。  在“位置”下，选择其他区域，如“东南亚”。
   ![多地域帮助图像 1][1]
3. 选择工作区，然后单击“登录到机器学习工作室”。
   ![多地域帮助图像 2][2]
4. 你现在已在其他区域中具有工作区，可以像使用其他任何工作区一样使用它。 若要在工作区之间切换，请查看屏幕的右上方。 单击下拉列表、选择区域，然后选择工作区。 每一项都位于该工作区区域。  例如，所有已从工作区创建的 Web 服务都将位于该工作区所在的区域。
   ![多地域帮助图像 3][3]

## <a name="open-an-experiment-from-gallery"></a>从库打开实验
如果从库打开实验，也可以选择要将实验复制到的区域。

![多地域帮助图像 4][4a]

## <a name="web-service-management"></a>Web 服务管理
若要以编程方式管理 Web 服务（如重新训练），请使用特定于区域的地址：

* https://asiasoutheast.management.azureml.net
* https://europewest.management.azureml.net

### <a name="things-to-note"></a>需要注意的事项
1. 你只能采用这种方式在属于同一区域的工作区之间复制实验。 如果需要在不同的区域中跨工作区复制实验，可以使用 [PowerShell](http://aka.ms/amlps) commandlet [*Copy-AmlExperiment*](https://github.com/hning86/azuremlps/blob/master/README.md#copy-amlexperiment) 来完成。 另一个解决方法是在未列出的模式下将实验发布到库，然后在其他区域的工作区中打开它。
2. 区域选择器一次只会显示一个区域的工作区。  
3. 免费工作区或来宾访问（匿名）工作区将在美国中南部创建并托管  
4. 从东南亚工作区部署的 Web 服务也将在东南亚托管。  

## <a name="more-information"></a>详细信息
在 [Azure 机器学习论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=MachineLearning)上提问。

<!--Image references-->
[1]: ./media/machine-learning-multi-geo/multi-geo_1.png
[2]: ./media/machine-learning-multi-geo/multi-geo_2.png
[3]: ./media/machine-learning-multi-geo/multi-geo_3.png
[4a]: ./media/machine-learning-multi-geo/multi-geo_4a.png



<!--HONumber=Dec16_HO2-->


