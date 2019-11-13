---
title: 在定型运行中使用机密
titleSuffix: Azure Machine Learning
description: 使用工作区 Key Vault 将秘密传递到定型运行
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: f4420824192ff3fd967cb6676cbe1de81ce7ad4c
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953914"
---
# <a name="use-secrets-in-training-runs"></a>在定型运行中使用机密
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何安全地运行训练中的机密。 例如，若要连接到外部数据库以查询定型数据，则需要将用户名和密码传递到远程运行上下文。 将此类值编码为明文中的定型脚本是不安全的，因为它会公开机密。 

相反，Azure 机器学习工作区已[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)为关联资源。 此 Key Vault 可用于通过 Azure 机器学习 Python SDK 中的一组 Api 安全地将机密传递到远程运行。

使用机密的基本流程是：
 1. 在本地计算机上，登录到 Azure 并连接到你的工作区。
 2. 在本地计算机上，在 "工作区 Key Vault 中设置机密。
 3. 提交远程运行。
 4. 在远程运行中，从密钥值获取密钥并使用它。

## <a name="set-secrets"></a>设置机密

在 Azure 机器学习 Python SDK 中， [Keyvault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py)类包含用于设置机密的方法。 在本地 Python 会话中，首先获取对工作区 Key Vault 的引用，然后使用[set_secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-)方法通过名称和值设置机密。

```python
from azureml.core import Workspace
import os

ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

不要将机密值放在 Python 代码中，因为它不安全地将其以明文形式存储在文件中。 相反，请从环境变量（例如 Azure DevOps 生成机密或交互式用户输入）获取机密值。

可以使用[list_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#list-secrets--)方法列出机密名称。 如果该名称已存在，则__set_secret__方法会更新该密钥值。

## <a name="get-secrets"></a>获取机密

在本地代码中，可以使用[Get_secret Keyvault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#get-secret-name-)方法按名称获取机密值。

在使用[试验](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#submit-config--tags-none----kwargs-)提交的运行中，使用[get_secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secret-name-)方法。 由于提交的运行可感知其工作区，因此此方法会将工作区实例化快捷方式并直接返回密钥值。

```python
# Code in submitted run
from azureml.core import Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

请注意不要通过编写或打印机密值来公开机密值。

Set 和 get 方法还具有批处理版本[set_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secrets-secrets-batch-)和[get_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secrets-secrets-) ，可同时访问多个机密。

## <a name="next-steps"></a>后续步骤

 * [查看示例笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [了解 Azure 机器学习的企业安全性](concept-enterprise-security.md)
