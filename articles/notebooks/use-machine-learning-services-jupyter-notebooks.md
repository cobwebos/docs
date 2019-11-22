---
title: 使用 Azure Notebooks 中的 Azure 机器学习
description: 概述了可用于 Azure Notebooks Azure 机器学习的示例笔记本。
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: e3c4fbdf35bf7ea1f4dddbceb9d7235c67ed13a4
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277462"
---
# <a name="use-azure-machine-learning-in-a-notebook"></a>使用笔记本中的 Azure 机器学习

Azure Notebooks 已预先配置为使用[Azure 机器学习](/azure/machine-learning/service/)所需的环境。 可以轻松地将示例项目复制到 Notebook 帐户，以浏览各种机器学习场景。

## <a name="clone-the-sample-into-your-account"></a>将示例克隆到帐户

1. 登录 [Azure Notebooks](https://notebooks.azure.com/)。
1. 选择 "**我的项目**" 以切换到 "项目" 仪表板。
1. 选择 "**上传 Github**存储库" （向上箭头）按钮，打开 "**上传 github 存储库**" 弹出窗口。
1. 在弹出项中输入 "`Azure/MachineLearningNotebooks` 在**GitHub 存储库**中，在"**项目名称**"中提供项目的名称，如" Azure 机器学习 "，在"**项目 ID**"中提供一个标识符; 如果需要，请清除"**公开**"，然后选择"**导入**"。

    ![将 Azure 机器学习笔记本示例导入 Notebooks 帐户](media/azureml-import-project.png)

1. 一两分钟后，Azure Notebooks 会自动转到新项目的仪表板。

## <a name="run-a-sample-notebook"></a>运行示例笔记本

1. 选择 00 - configuration.ipynb 启动笔记本的配置部分，并按照其说明创建 Azure 机器学习工作区。

    - 因为 Azure Notebooks 已包含必要的 Python 包，所以可以只运行先决条件的第 2 步中的代码片段来验证 Azure ML SDK 版本。

1. 配置完成后，选择 " **01. 入门**" 以打开包含十三个不同示例笔记本的文件夹，其中每个示例都一目了然。

## <a name="next-steps"></a>后续步骤

Azure 机器学习文档包含各种其他资源，这些资源可指导你使用笔记本中的机器学习：

- [快速入门：使用 Python 开始使用 Azure 机器学习](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [教程 #1：使用 Azure 机器学习训练图像分类模型](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [教程 #2：在 Azure 容器实例（ACI）中部署图像分类模型](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [教程：在 Azure 机器学习中使用自动化机器学习训练分类模型](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

另请参阅[用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 一文。
