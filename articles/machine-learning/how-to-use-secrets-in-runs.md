---
title: 培训中的身份验证机密
titleSuffix: Azure Machine Learning
description: 使用工作区密钥保管库将机密安全传递到训练运行
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: e984c0c43dcc47c3e11a36f3d5c32bf2ddb9973a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90902335"
---
# <a name="use-authentication-credential-secrets-in-azure-machine-learning-training-runs"></a>在 Azure 机器学习训练运行中使用身份验证凭据机密


本文介绍如何在训练运行中安全使用机密。 身份验证信息（例如用户名和密码）是机密。 例如，如果连接到外部数据库来查询训练数据，则需要将用户名和密码传递到远程运行上下文。 将此类值编码为明文中的训练脚本是不安全的，因为该过程会暴露机密。 

相反，你的 Azure 机器学习工作区有一个称为 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) 的关联资源。 使用此密钥保管库，可通过 Azure 机器学习 Python SDK 中的一组 API 安全将机密传递给远程运行。

使用机密的标准流程是：
 1. 在本地计算机上，登录到 Azure 并连接到你的工作区。
 2. 在本地计算机上，在“工作区密钥保管库”中设置机密。
 3. 提交远程运行。
 4. 在远程运行中，从密钥保管库获取机密并使用它。

## <a name="set-secrets"></a>设置机密

在 Azure 机器学习中，[Keyvault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py&preserve-view=true) 类包含用于设置机密的方法。 在本地 Python 会话中，首先获取对工作区密钥保管库的引用，然后使用 [`set_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#&preserve-view=trueset-secret-name--value-) 方法通过名称和值来设置机密。 如果名称已存在，__set_secret__ 方法会更新该密钥值。

```python
from azureml.core import Workspace
from azureml.core import Keyvault
import os


ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

不要将机密值放在 Python 代码中，因为将其以明文形式存储在文件中不安全。 应从环境变量中获取机密值（例如 Azure DevOps 生成机密）或从交互式用户输入中获取机密值。

你可以使用 [`list_secrets()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#&preserve-view=truelist-secrets--) 方法列出机密名称，此方法还有一个批处理版本 [set_secrets()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#&preserve-view=trueset-secrets-secrets-batch-)，允许你一次设置多个机密。

## <a name="get-secrets"></a>获取机密

在本地代码中，可以使用 [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#&preserve-view=trueget-secret-name-) 方法通过名称获取机密值。

对于提交了 [`Experiment.submit`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#&preserve-view=truesubmit-config--tags-none----kwargs-) 的运行，请将 [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#&preserve-view=trueget-secret-name-) 方法与 [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py&preserve-view=true) 类结合使用。 由于提交的运行知晓其工作区，因此此方法会绕过工作区实例化，直接返回密钥值。

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

请注意不要将机密值写出或打印出来而导致机密值暴露。

还有一个批处理版本 [get_secrets()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#&preserve-view=trueget-secrets-secrets-)，用于同时访问多个机密。

## <a name="next-steps"></a>后续步骤

 * [查看示例笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [了解 Azure 机器学习的企业安全性](concept-enterprise-security.md)
