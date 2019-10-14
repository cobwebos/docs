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
ms.date: 09/26/2019
ms.openlocfilehash: 85d1c1cd294bfc02a2e0e327073bb6a80366548b
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841103"
---
1. 按照 [Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) 中的说明安装适用于 Python 的 Azure 机器学习 SDK

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
