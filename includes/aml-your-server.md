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
ms.openlocfilehash: 8d8b314965253dc00b39d0b068b1d6fb3e4aa471
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58395563"
---
1. 使用[创建 Azure 机器学习服务工作区](../articles/machine-learning/service/setup-create-workspace.md#portal)中的说明创建 Miniconda 环境、创建工作区并编写工作区配置文件 (**aml_config/config.json**)。

1. 克隆 [GitHub 存储库](https://aka.ms/aml-notebooks)。

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 使用下述方法之一，添加工作区配置文件：
    * 将使用先决条件快速入门创建的 **aml_config/config.json** 文件复制到克隆的目录中。
    * 使用 [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) 中的代码创建新的工作区。
1. 从克隆目录启动 notebook 服务器。
    
    ```shell
    jupyter notebook
    ```