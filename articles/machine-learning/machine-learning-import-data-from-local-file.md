---
title: "将数据从本地文件导入机器学习工作室 | Microsoft Docs"
description: "如何将训练数据从本地文件导入 Azure 机器学习工作室。"
keywords: "导入数据,数据格式,数据类型,数据源,训练数据"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: c0dd9e90-23c4-4f64-8b8f-489ad79f047b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2016
ms.author: garye;bradsev
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d4ab1fd19d0c865e8920ae3079df1683aaa4be7d


---
# <a name="import-your-training-data-into-azure-machine-learning-studio-from-a-local-file"></a>将训练数据从本地文件导入 Azure 机器学习工作室
[!INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]

若要在机器学习工作室中使用自己的数据，可以提前从本地硬盘上载数据文件，以便在工作区中创建数据集模块。 

## <a name="import-data-from-a-local-file"></a>从本地文件导入数据
通过执行以下操作，可从本地硬盘导入数据：

1. 单击机器学习工作室窗口底部的“+新建”。
2. 选择“数据集”和“从本地文件”。
3. 在“上载新的数据集”对话框中，浏览到要上载的文件
4. 输入名称、标识数据类型，还可选择输入描述。 建议输入描述：这样可以记录有关数据的任何特征，以便将来使用数据时能够记起。
5. 使用复选框“这是现有数据集的新版本”可以使用新数据更新现有数据集。 只需单击此复选框，然后输入现有数据集的名称即可。

上载期间，你将看到一条消息指示文件正在上载。 上载时间取决于数据大小和连接到服务的速度。
如果你知道文件将花费很长时间，等待时可以在机器学习工作室中执行其他操作。 但是，关闭浏览器将导致数据上载失败。

数据上载后，将存储在数据集模块中，并且可用于工作区中的任何实验。
编辑实验时，可以在模块选项板中的“已保存数据集”列表下的“我的数据集”列表中找到已创建的数据集。 如果希望将数据集用于进一步分析和机器学习，可以将数据集拖放到实验画布。




<!--HONumber=Nov16_HO3-->


