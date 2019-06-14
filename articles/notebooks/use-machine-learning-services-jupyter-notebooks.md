---
title: 在 Azure Notebooks 中使用 Azure 机器学习服务
description: 用于 Azure 机器学习服务的示例笔记本的概述，其可与 Azure Notebooks 配合使用。
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 0dc4fc31-ae1c-422c-ac34-7b025e6651b4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 2ef327721fd42e5274381834721fd987ec7e9d75
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60240505"
---
# <a name="use-azure-machine-learning-service-in-a-notebook"></a>在笔记本中使用 Azure 机器学习服务

Azure Notebooks 预先配置了使用 [Azure 机器学习服务](/azure/machine-learning/service/)所需的环境。 可以轻松地将示例项目复制到 Notebook 帐户，以浏览各种机器学习场景。

## <a name="clone-the-sample-into-your-account"></a>将示例克隆到帐户

1. 登录 [Azure Notebooks](https://notebooks.azure.com/)。
1. 选择“我的项目”以导航到项目仪表板  。
1. 选择“上传 GitHub 存储库”（向上键）按钮，以打开“上传 GitHub 存储库”弹出窗口   。
1. 在弹出窗口中，在“GitHub 存储库”中输入 `Azure/MachineLearningNotebooks`，在“项目名称”中为项目提供名称（如“Azure 机器学习服务”），在“项目 ID”中提供标识符，并且如果需要，可以清除“公共”，然后选择“导入”      。

    ![将 Azure 机器学习笔记本示例导入 Notebooks 帐户](media/azureml-import-project.png)

1. 一两分钟后，Azure Notebooks 会自动转到新项目的仪表板。

## <a name="run-a-sample-notebook"></a>运行示例笔记本

1. 选择 00 - configuration.ipynb 启动笔记本的配置部分，并按照其说明创建 Azure 机器学习工作区  。

    - 因为 Azure Notebooks 已包含必要的 Python 包，所以可以只运行先决条件的第 2 步中的代码片段来验证 Azure ML SDK 版本。

1. 配置完成后，选择 01.getting-started 导航到包含 13 个不同示例笔记本的文件夹，每个示例笔记本都容易理解  。

## <a name="next-steps"></a>后续步骤

Azure 机器学习服务文档包含各种其他资源，可利用这些资源了解如何在笔记本中使用机器学习服务：

- [快速入门：通过 Python 开始使用 Azure 机器学习](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [教程 #1：使用 Azure 机器学习服务训练图像分类模型](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [教程 #2：在 Azure 容器实例 (ACI) 中部署映像分类模型](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [教程：使用 Azure 机器学习服务中的自动化机器学习训练分类模型](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

另请参阅[用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 一文。
