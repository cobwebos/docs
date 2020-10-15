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
ms.date: 03/05/2020
ms.openlocfilehash: ff449626ce528cfe0218a95330a567303c547e5f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "79486056"
---
1. 按照 [Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) 中的说明安装适用于 Python 的 Azure 机器学习 SDK

1. 创建 [Azure 机器学习工作区](../articles/machine-learning/how-to-manage-workspace.md)。

1. 编写一个[配置文件](../articles/machine-learning/how-to-configure-environment.md#workspace) 文件 (**aml_config/config.json**)。

1. 克隆 [GitHub 存储库](https://aka.ms/aml-notebooks)。

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 从克隆目录启动 notebook 服务器。

    ```bash
    jupyter notebook
    ```