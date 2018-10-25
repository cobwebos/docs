---
title: Azure 机器学习服务的已知问题和故障排除
description: 获取已知问题、解决方法和故障排除的列表
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 02cee5a3e088c919ec94aee6f46ef6f428b9bb48
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249411"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Azure 机器学习服务的已知问题和故障排除
 
本文可帮助你查找和更正使用 Azure 机器学习服务时遇到的错误或失败。 

## <a name="sdk-installation-issues"></a>SDK 安装问题

**错误消息：无法卸载 'PyYAML'** 

PyYAML 是 distutils 安装的项目。 因此，在部分卸载的情况下，我们无法准确确定哪些文件属于它。 若要在忽略此错误的同时继续安装 SDK，请使用：
```Python 
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

## <a name="image-building-failure"></a>映像生成失败

部署 Web 服务时映像生成失败。 解决方法是将“pynacl==1.2.1”作为 pip 依赖项添加到 Conda 文件以进行映像配置。  

## <a name="pipelines"></a>管道
在不更改脚本或参数的情况下，连续多次调用 PythonScriptStep 时发生错误。 解决方法是重新生成 PipelineData 对象。

## <a name="fpgas"></a>FPGA
你将无法在 FPGA 上部署模型，直到已请求并获得 FPGA 配额批准为止。 若要请求访问权限，请填写配额请求表单： https://aka.ms/aml-real-time-ai

## <a name="databricks"></a>Databricks

Databricks 和 Azure 机器学习问题。

1. Databricks 群集建议：
   
   使用 Python 3 创建版本为 4.x 的 Azure Databricks 群集。 我们建议使用高并发群集。
 
1. 安装更多程序包时，AML SDK 会在 Databricks 上安装失败。

   某些包（如 `psutil upgrade libs`）可能会导致冲突。 为了避免安装错误，请通过冻结 lib 版本来安装包。 此问题与 Databricks 有关，与 AML SDK 无关。 示例：
   ```python
   pstuil cryptography==1.5 pyopenssl==16.0.0 ipython=2.2.0
   ```

## <a name="diagnostic-logs"></a>诊断日志
如果在请求帮助时可以提供诊断信息，有时会很有帮助。 下面是日志文件所在的位置：

## <a name="resource-quotas"></a>资源配额

了解使用 Azure 机器学习时可能遇到的[资源配额](how-to-manage-quotas.md)。

## <a name="get-more-support"></a>获取更多支持

可以通过技术支持、论坛等提交支持请求并获取帮助。 [了解详细信息...](support-for-aml-services.md)
