---
title: 已知问题与故障排除
titleSuffix: Azure Machine Learning service
description: 获取 Azure 机器学习服务的已知问题、解决方法和故障排除的列表。
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: article
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: c327d973170a4556471663c3bea9dcae9b5794fb
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238605"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Azure 机器学习服务的已知问题和故障排除

本文可帮助你查找和更正使用 Azure 机器学习服务时遇到的错误或失败。

## <a name="sdk-installation-issues"></a>SDK 安装问题

**错误消息：无法卸载 'PyYAML'**

适用于 Python 的 Azure 机器学习 SDK：PyYAML 是 distutils 安装的项目。 因此，在部分卸载的情况下，我们无法准确确定哪些文件属于它。 若要在忽略此错误的同时继续安装 SDK，请使用：
```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

## <a name="trouble-creating-azure-machine-learning-compute"></a>创建 Azure 机器学习计算时发生故障

如果用户在 GA 发布之前已通过 Azure 门户创建了自己的 Azure 机器学习工作区，则他们很可能无法在该工作区中创建 Azure 机器学习计算。 可对服务提出支持请求，也可通过门户或 SDK 创建新的工作区以立即解除锁定。

## <a name="image-building-failure"></a>映像生成失败

部署 Web 服务时映像生成失败。 解决方法是将“pynacl==1.2.1”作为 pip 依赖项添加到 Conda 文件以进行映像配置。

## <a name="deployment-failure"></a>部署失败

如果你观察到 'DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL:9> - 请将部署中使用的 VM 的 SKU 更改为具有更高内存的 SKU。

## <a name="fpgas"></a>FPGA
你将无法在 FPGA 上部署模型，直到已请求并获得 FPGA 配额批准为止。 若要请求访问权限，请填写配额请求表单： https://aka.ms/aml-real-time-ai

## <a name="databricks"></a>Databricks

Databricks 和 Azure 机器学习问题。

1. 安装更多程序包时，AML SDK 会在 Databricks 上安装失败。

   某些包（如 `psutil`）可能会导致冲突。 为了避免安装错误，请通过冻结 lib 版本来安装包。 此问题与 Databricks 有关，与 Azure 机器学习服务 SDK 无关 - 你可能还会遇到其他库。 示例：
   ```python
   pstuil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
   ```
   或者，如果一直面临 Python 库的安装问题，可以使用 init 脚本。 这种方法不是官方支持的方法。 可参考[此文档](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)。

2. 在 Databricks 上使用自动机器学习时，如果要取消运行并启动新的实验运行，请重启 Azure Databricks 群集。

3. 在自动 ml 设置中，如果迭代超过 10 个，请在提交运行时将 show_output 设置为 False。


## <a name="azure-portal"></a>Azure 门户
如果直接通过 SDK 或门户的共享链接查看工作区，则将无法在扩展程序中查看包含订阅信息的常规概述页。 也将无法切换到另一个工作区。 如果需要查看其他工作区，解决方法是直接转到 [Azure 门户](https://portal.azure.com)并搜索工作区名称。

## <a name="diagnostic-logs"></a>诊断日志
如果在请求帮助时可以提供诊断信息，有时会很有帮助。
下面是日志文件所在的位置：

## <a name="resource-quotas"></a>资源配额

了解使用 Azure 机器学习时可能遇到的[资源配额](how-to-manage-quotas.md)。

## <a name="get-more-support"></a>获取更多支持

可以通过技术支持、论坛等提交支持请求并获取帮助。 [了解详细信息...](support-for-aml-services.md)
