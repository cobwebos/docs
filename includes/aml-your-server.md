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
ms.openlocfilehash: 18ba86ce7876ba8275eb4853e4fc9ea0f35fa186
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302171"
---
1. 完成 [Azure 机器学习 Python 快速入门](../articles/machine-learning/service/quickstart-create-workspace-with-python.md)，以便安装 SDK 并创建工作区。  可以随意跳过“使用笔记本”部分。
1. 克隆 [GitHub 存储库](https://aka.ms/aml-notebooks)。

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 使用下述方法之一，添加工作区配置文件：
    * 将通过先决条件快速入门创建的 **aml_config\config.json** 文件复制到克隆的目录中。
    * 使用 [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) 中的代码创建新的工作区。
1. 从克隆目录启动 notebook 服务器。
    
    ```shell
    jupyter notebook
    ```