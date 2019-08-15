---
title: include 文件
description: include 文件
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 01/25/2019
ms.openlocfilehash: 09a3cc5a623be2ee5a9d50204f0902ca9f400a76
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857319"
---
1. [创建 Azure 机器学习服务工作区](../articles/machine-learning/service/how-to-manage-workspace.md)。

1. 克隆 [GitHub 存储库](https://aka.ms/aml-notebooks)。

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 使用下述方法之一，将工作区配置文件添加到克隆目录：

    * 在[Azure 门户](https://ms.portal.azure.com)中，选择工作区的“概览”  部分中的“下载 config.json”  。 

    ![下载 config.json](./media/aml-dsvm-server/download-config.png)

    * 在克隆的目录中使用 [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) 笔记本中的代码创建新的工作区。

1. 从克隆目录启动 notebook 服务器。

    ```shell
    jupyter notebook
    ```