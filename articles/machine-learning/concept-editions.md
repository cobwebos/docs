---
title: Azure 机器学习 Enterprise Edition 和 Basic Edition
description: 了解 Azure 机器学习两个版本之间的差异。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: j-martens
ms.author: jmartens
ms.date: 06/11/2020
ms.openlocfilehash: fbe3f0109ca3af2cf9ff44061c7882e6bd72c8a6
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88508596"
---
# <a name="enterprise-preview-and-basic-editions-of-azure-machine-learning"></a>企业 (预览版) 和基本版 Azure 机器学习 

Azure 机器学习提供了两个版本，专为你的机器学习需求提供。 这些版本确定开发人员和数据科学家的工作区中可用的机器学习工具。

## <a name="choose-an-edition"></a>选择版本

你始终可以在创建工作区时分配版本。 客户负责在此期间因计算和其他 Azure 资源产生的成本。 了解如何[管理 Azure 机器学习的成本](concept-plan-manage-cost.md)。

了解如何将 [基本工作区升级到 Enterprise (预览版) edition](how-to-manage-workspace.md#upgrade)。 

## <a name="whats-in-each-edition"></a>每个版本中的内容

### <a name="data-for-machine-learning-capabilities"></a>机器学习功能的数据  

| 功能                     | 版本                 |
|------------------------------------------------------------------------------------|:-----------:|
| 标记：在工作室 (Web) 中[创建和管理标记项目](tutorial-labeling.md)                                                | 全部                     |
| 标记：工作室 (Web) 中的标记工具                                    | 全部                     |
| 标记：使用私人劳动力                               | 全部                     |
| 标记：[ML 辅助映像分类和对象检测](how-to-label-images.md)                  | 仅限 Enterprise Edition |
| 数据集 + 数据存储：使用 Python 创建和管理                       | 全部                     |
| 数据集 + 数据存储：在工作室 (Web) 中创建和管理                         | 全部                     |
| 偏移：使用 Python 查看和管理数据集监视器                           | 全部                     |
| 偏移：在工作室 (Web) 中查看和管理数据集监视器                            | 仅限 Enterprise Edition |


<br/>
<br/>

### <a name="automated-training-capabilities-automl"></a>自动训练功能 (AutoML)

| 功能    | 版本                 |
|------------------------------------------------------------------------------------|:-----------:|
| 在笔记本中创建和运行 [AutoML 试验](how-to-configure-auto-train.md)               | 全部                     |
| 在工作室 (Web) 中创建和运行 [AutoML 试验](how-to-use-automated-ml-for-ml-models.md)   | 仅限 Enterprise Edition |
| 行业领先的 AutoML 预测功能             | 仅限 Enterprise Edition |
| 支持深度学习和其他高级学习器 | 仅限 Enterprise Edition |
| 大型数据支持分类和回归任务（高达 100 GB）                     | 仅限 Enterprise Edition |


<br/>
<br/>

### <a name="responsible-machine-learning"></a>负责机器学习

| 功能    | 版本                 |
|------------------------------------------------------------------------------------|:-----------:|
| [模型可解释性](how-to-machine-learning-interpretability-automl.md)                                              | 全部                     |
| [差异隐私](how-to-differential-privacy.md)                          | 全部                     |
| 自定义标记以实现数据表    | 全部                     |
| 公平 AzureML 集成                                      | 全部                     |

<br/>
<br/>


### <a name="build-and-train-capabilities"></a>构建和训练功能

| 功能    | 版本                 |
|------------------------------------------------------------------------------------|:-----------:|
| Visual Studio Code 集成                                                     | 全部                     |
| 强化学习                                                             | 全部                     |
| 试验 UI                                                                 | 全部                     |
| Jupyter、JupyterLab 集成                                                    | 全部                     |
| Python SDK 支持                                                                 | 全部                     |
| R SDK 支持                                                                      | 全部                     |
| ML 管道：使用 Python 创建、运行和发布                           | 全部                     |
| ML 管道：使用 Python 创建、编辑和删除计划的管道运行| 全部                     |
| ML 管道：在 Python SDK 中创建管道终结点                                   | 全部                     |
| ML 管道：在工作室 (Web) 中查看运行详细信息                                              | 全部                     |
| ML 管道：在设计器中创建、运行、可视化和发布                  | 仅限 Enterprise Edition |
| ML 管道：在设计器中创建管道终结点 | 仅限 Enterprise Edition |
| 集成笔记本的托管计算实例                                 | 全部                     |


<br/>
<br/>

### <a name="deployment-and-model-management-capabilities"></a>部署和模型管理功能

| 功能                            | 版本                 |
|------------------------------------------------------------------------------------|:-----------:|
| 适用于机器学习和 Azure ML CLI 的 Azure DevOps 扩展                 | 全部                     |
| [事件网格集成](how-to-use-event-grid.md)                                                             | 全部                     |
| 将 Azure 流分析与 Azure 机器学习集成                       | 全部                     |
| 在 SDK 中创建 ML 管道                                                         | 全部                     |
| 批处理推理                                                                  | 全部                     |
| 基于 FPGA 的硬件加速模型                                             | 全部                     |
| 模型分析                                                                    | 全部                     |

<br/>
<br/>

### <a name="security-governance-and-control-capabilities"></a>安全、治理和控制功能

| 功能     | 版本                 |
|------------------------------------------------------------------------------------|:-----------:|
| Azure [RBAC) 支持 (基于角色的访问控制](how-to-assign-roles.md)                                           | 全部                     |
| [虚拟网络 (VNet)](how-to-enable-virtual-network.md) 计算支持                                         | 全部                     |
| 对终结点身份验证评分                                                    | 全部                     |
| [工作区专用链接](how-to-configure-private-link.md)                                                            | 全部                     |
| 跨工作区[配额管理](how-to-manage-quotas.md)                                                 | 仅限 Enterprise Edition |

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure 机器学习中提供的内容，请参阅[版本概述和定价页](https://azure.microsoft.com/pricing/details/machine-learning/)。 

了解如何[将 Basic 工作区升级到 Enterprise Edition](how-to-manage-workspace.md#upgrade)。 
