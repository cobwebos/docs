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
ms.openlocfilehash: 6c93d1243db1b3c4277a54cf71e10f6bbc648d26
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2019
ms.locfileid: "68846013"
---
- 安装了适用于 Python 的 Azure 机器学习 SDK。 按照 [Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) 中的说明执行以下操作：


1. 按照 [Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) 中的说明执行以下操作：
    * 创建 Miniconda 环境[创建和管理 Azure 机器学习服务工作区]
    * 安装用于 Python 的 Azure 机器学习 SDK

1. 创建 [Azure 机器学习服务工作区](../articles/machine-learning/service/how-to-manage-workspace.md)。

1. 编写一个[配置文件](../articles/machine-learning/service/how-to-configure-environment.md#workspace) 文件 (**aml_config/config.json**)。

1. 克隆 [GitHub 存储库](https://aka.ms/aml-notebooks)。

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 从克隆目录启动 notebook 服务器。

    ```shell
    jupyter notebook
    ```