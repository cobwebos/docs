---
title: 将文件中的数据导入 Azure 机器学习工作室 | Microsoft 文档
description: 了解如何将训练数据文件从硬盘驱动器上传到 Azure 机器学习工作室。 这会在工作区中创建一个数据集模块。
keywords: 导入数据, 数据格式, 数据类型, 数据源, 训练数据
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: c0dd9e90-23c4-4f64-8b8f-489ad79f047b
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 0365492b2814d686dd0bfa099e94717137b51725
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834764"
---
# <a name="import-training-data-from-a-file-on-your-hard-drive-into-machine-learning-studio"></a>将硬盘驱动器上某个文件中的训练数据导入机器学习工作室
[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

了解如何上传硬盘驱动器中的数据文件，将其用作 Azure 机器学习工作室中的训练数据。 导入数据文件后，工作区中便有一个可供使用的数据集模块。

## <a name="steps-to-import-data-from-a-local-file"></a>从本地文件导入数据的步骤
若要从本地硬盘驱动器导入数据，请执行以下操作：

1. 单击机器学习工作室窗口底部的“+新建”。
2. 选择“数据集”和“从本地文件”。
3. 在“上传新的数据集”对话框中，浏览到要上传的文件
4. 输入名称、标识数据类型，还可选择输入描述。 建议输入描述：这样可以记录有关数据的任何特征，以便将来使用数据时能够记起。
5. 使用复选框“这是现有数据集的新版本”可以使用新数据更新现有数据集。 单击此复选框，并输入现有数据集的名称即可。

![上传新的数据集](./media/import-data-from-local-file/upload-dataset.png)

上传期间，会看到一条消息指示文件正在上传。 上传时间取决于数据大小和连接到服务的速度。 如果知道文件将花费很长时间，等待时可以在机器学习工作室中执行其他操作。 但是，关闭浏览器将导致数据上传失败。

## <a name="dataset-module-is-ready-for-use"></a>数据集模块可供使用
数据上传后，将存储在数据集模块中，并且可用于工作区中的任何实验。

编辑实验时，可以在模块选项板中的“已保存数据集”列表下的“我的数据集”列表中找到已创建的数据集。 如果希望将数据集用于进一步分析和机器学习，可以将数据集拖放到实验画布。
