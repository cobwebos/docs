---
title: 公共区域和主权区域之间的奇偶校验
titleSuffix: Azure Machine Learning
description: 某些 Azure 机器学习功能，如公共预览功能，只能在公有云区域中使用。 本文列出了 Azure 政府版、Azure 德国和 Azure 中国世纪互联地区的可用功能。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: andzha
author: Anurzeuii
ms.date: 08/24/2020
ms.custom: references_regions
ms.openlocfilehash: 886e45e92fb3a882de167b5c59a9b5ee09a9c430
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89657567"
---
# <a name="azure-machine-learning-sovereign-cloud-parity"></a>Azure 机器学习主权云奇偶校验

了解主权云区域中可用的 Azure 机器学习功能。 

在全球 Azure 区域列表中，有多个 "主权" 区域提供特定的市场。 例如，Azure 政府部门和 Azure 中国世纪互联地区。 目前 Azure 机器学习部署到以下主权云区域中：

* Azure 政府区域 **-亚利桑那州** 和 **美国-弗吉尼亚州**。
* Azure 中国世纪互联区域 **（中国）-东-2**。

> [!TIP]
> 为了区分主权和非主权区域，本文将使用术语 " __公有云__ " 来指代非主权区域。

我们的目标是在公有云和主权区域之间提供最大的奇偶校验。 所有 Azure 机器学习功能都将在 **正式发布的30天内的** 这些区域中提供， (公有云中的公开上市) 。 我们还在这些区域中启用了选择数量的预览功能。 下面显示了主权和公有云之间的当前奇偶校验差异。

## <a name="azure-government"></a>Azure Government 

| 功能 | 公有云状态  | 美国-弗吉尼亚州 | 美国-亚利桑那| 
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|:-------------:|
| **自动化机器学习** | | | |
| 在笔记本中创建和运行试验                                    | GA                   | YES                | YES         |
| 在 studio web 体验中创建和运行试验                        | 公共预览版       | YES                | YES         |
| 行业领先的预测功能                                  | GA                   | YES                | YES         |
| 支持深度学习和其他高级学习器                      | GA                   | YES                | YES         |
| 大数据支持 (多达 100 GB)                                           | 公共预览版       | YES                | YES         |
| Azure Databricks 集成                                              | GA                   | 是                 | 是          |
| SQL、CosmosDB 和 HDInsight 集成                                   | GA                   | YES                | YES         |
| **机器学习管道** |   |  | | 
| 使用 Azure ML SDK 创建、运行和发布管道                   | GA                   | YES                | YES         |
| 使用 Azure ML SDK 创建管道终结点                           | GA                   | YES                | YES         |
| 使用 Azure ML SDK 创建、编辑和删除预定的管道运行 | GA                   | [               | [        |
| 在工作室中查看管道运行详细信息                                        | GA                   | YES                | YES         |
| 在 Azure ML 设计器中创建、运行、可视化和发布管道          | 公共预览版       | YES                | YES         |
| Azure Databricks 与 ML 管道集成                             | GA                   | 是                 | 是          |
| 在 Azure ML 设计器中创建管道终结点                             | 公共预览版       | YES                | YES         |
| **集成笔记本** |   |  | | 
| 工作区笔记本和文件共享                                        | GA                   | YES                | YES         |
| R 和 Python 支持                                                       | GA                   | YES                | YES         |
| 虚拟网络支持                                                    | 公共预览版       | 是                 | 是          |
| **计算实例** |   |  | | 
| 集成笔记本的托管计算实例                         | GA                   | YES                | YES         |
| Jupyter、JupyterLab 集成                                            | GA                   | YES                | YES         |
| 虚拟网络 (VNet) 支持                                             | 公共预览版       | YES                | YES         |
| **SDK 支持** |  |  | | 
| R SDK 支持                                                              | 公共预览版       | YES                | YES         |
| Python SDK 支持                                                         | GA                   | YES                | YES         |
| **安全性** |   | | | 
| 虚拟网络 (VNet) 支持培训                                | GA                   | YES                | YES         |
| 虚拟网络 (VNet) 支持推理                               | GA                   | YES                | YES         |
| 对终结点身份验证评分                                            | 公共预览版       | YES                | YES         |
| 工作区专用链接                                                     | 公共预览版       | 是                 | 是          |
| VNet 后的 ACI                                                            | 公共预览版       | 是                 | 是          |
| VNet 后的 ACR                                                            | 公共预览版       | 是                 | 是          |
| AKS 群集的专用 IP                                                  | 公共预览版       | 是                 | 是          |
| **计算** |   | | |
| 跨工作区的配额管理                                         | GA                   | YES                | YES         |
| **用于机器学习的数据** |   | | |
| 从 SDK 创建、查看或编辑数据集和数据存储                  | GA                   | YES                | YES         |
| 从 UI 创建、查看或编辑数据集和数据存储                   | GA                   | YES                | YES         |
| 查看、编辑或删除 SDK 中的数据集偏移监视器                   | 公共预览版       | YES                | YES         |
| 查看、编辑或删除 UI 中的数据集偏移监视器                    | 公共预览版       | YES                | YES         |
| **机器学习生命周期** |   | | |
| 模型分析                                                            | GA                   | YES                | PARTIAL     |
| 适用于机器学习的 Azure DevOps 扩展 & Azure ML CLI         | GA                   | YES                | YES         |
| 基于 FPGA 的硬件加速模型                                     | GA                   | 是                 | 是          |
| Visual Studio Code 集成                                             | 公共预览版       | 是                 | 是          |
| 事件网格集成                                                     | 公共预览版       | 是                 | 是          |
| 将 Azure 流分析与 Azure 机器学习集成               | 公共预览版       | 是                 | 是          |
| **标记** |   | | |
| 标记项目管理门户                                        | GA                   | YES                | YES         |
| Labeler 门户                                                            | GA                   | YES                | YES         |
| 使用专用劳动力标签                                          | GA                   | YES                | YES         |
| ML 辅助标签 (图像分类和对象检测)            | 公共预览版       | YES                | YES         |
| **负责的 ML** |   | | |
| UI 的可说明性                                                       | 公共预览版       | 是                 | 是          |
| 差异隐私 WhiteNoise 工具包                                    | OS                  | 是                 | 是          |
| 用于实现数据表的 Azure 机器学习中的自定义标记              | GA                   | 是                 | 是          |
| 公平 AzureML 集成                                               | 公共预览版       | 是                 | 是          |
| Interpretability SDK                                                      | GA                   | YES                | YES         |
| **培训** |   | | |
| 试验日志流式处理                                              | GA                   | YES                | YES         |
| 强化学习                                                     | 公共预览版       | 是                 | 是          |
| 试验 UI                                                         | GA                   | YES                | YES         |
| .NET integration ML.NET 1。0                                                | GA                   | YES                | YES         |
| **推理** |   | | |
| 批处理推理                                                          | GA                   | YES                | YES         |
| Data Box Edge 与 FPGA                                                    | 公共预览版       | 是                 | 是          |
| **其他** |   | | |
| 开放数据集                                                              | 公共预览版       | YES                | YES         |
| 自定义认知搜索                                                    | 公共预览版       | YES                | YES         |
| 许多模型                                                                | 公共预览版       | 是                 | 是          |


### <a name="azure-government-scenarios"></a>Azure 政府方案

| 方案                                                    | 美国-弗吉尼亚州 | 美国-亚利桑那| 限制  |
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|-------------|
| **常规安全设置** |   | | |
| 服务之间的专用网络通信                                     | 是 | 是 | 当前没有专用链接 | 
| 禁用/控制 internet 访问 (入站和出站) 和特定 VNet | PARTIAL| PARTIAL   | VNet 后的 ACR 在 Azure 政府版中不可用-对 ACI 进行双重检查 | 
| 所有关联资源/服务的放置  | YES | YES |  |
| 静态加密和传输中的加密。                                                 | YES | YES |  |
| 根和 SSH 对计算资源的访问权限。                                          | YES | YES |  |
| 维护部署系统 (实例、终结点等 ) 的安全性，包括 endpoint protection、修补和日志记录 |  PARTIAL|  PARTIAL |VNet 和私有终结点后的 ACI 当前不可用 |                                  
| 控制 (禁用/限制/限制) 使用 ACI/AKS 集成                    | PARTIAL| PARTIAL |VNet 和私有终结点后的 ACI 当前不可用|
| 基于角色的访问控制 (RBAC) -自定义角色创建                           | YES | YES |  |
| 控制对 ML 服务使用的 ACR 映像的访问 (Azure 提供/维护和自定义)   |PARTIAL|  PARTIAL | Azure 政府版在专用终结点和 VNet 之后不受支持 |
| **常规机器学习服务使用情况** |  | | |
| 能够使用开发环境构建模型、训练该模型、将其作为终结点托管并通过 webapp 使用     | YES | YES |  |
| 能够从 ADLS (Data Lake Storage 请求数据)                                  |YES | YES |  |
| 能够从 Azure Blob 存储提取数据                                       |YES | YES |  |



### <a name="additional-azure-government-limitations"></a>其他 Azure 政府限制

* 对于 Azure 机器学习计算实例，Azure 政府版中未提供刷新持续时间超过24小时的令牌的功能。
* 模型分析不支持美国-亚利桑那地区的4个 Cpu。   
* 如果笔记本的示例需要访问公共数据，则可能无法在 Azure 政府版中使用。
* IP 地址： VNet 中使用的 CLI 命令 [和强制隧道](how-to-secure-training-vnet.md#forced-tunneling) 说明不返回 IP 范围。 改为使用 azure 政府版的 [AZURE IP 范围和服务标记](https://www.microsoft.com/download/details.aspx?id=57063) 。
* 对于预定管道，还提供基于 blob 的触发器机制。 CMK 工作区不支持此机制。 若要为 CMK 工作区启用基于 blob 的触发器，必须执行其他设置。 有关详细信息，请参阅 [从逻辑应用触发机器学习管道的运行](how-to-trigger-published-pipeline.md)。
* 防火墙：使用 Azure 政府区域时，将以下其他主机添加到防火墙设置：

    * 对于亚利桑那，使用： `usgovarizona.api.ml.azure.us`
    * 对于弗吉尼亚州使用： `usgovvirginia.api.ml.azure.us`
    * 对于这两个： `graph.windows.net` 


## <a name="azure-china-21vianet"></a>Azure 中国世纪互联 

| 功能                                       | 公有云状态 | CH-东-2 | CH-北-3 |
|----------------------------------------------------------------------------|:------------------:|:--------------------:|:-------------:|
| **自动化机器学习** |    | | |
| 在笔记本中创建和运行试验                                    | GA               | YES       | 空值        |
| 在 studio web 体验中创建和运行试验                        | 公共预览版   | YES       | 空值        |
| 行业领先的预测功能                                  | GA               | YES       | 空值        |
| 支持深度学习和其他高级学习器                      | GA               | YES       | 空值        |
| 大数据支持 (多达 100 GB)                                           | 公共预览版   | YES       | 空值        |
| Azure Databricks 集成                                              | GA               | 是        | 空值        |
| SQL、CosmosDB 和 HDInsight 集成                                   | GA               | YES       | 空值        |
| **机器学习管道** |    | | |
| 使用 Azure ML SDK 创建、运行和发布管道                   | GA               | YES       | 空值        |
| 使用 Azure ML SDK 创建管道终结点                           | GA               | YES       | 空值        |
| 使用 Azure ML SDK 创建、编辑和删除预定的管道运行 | GA               | YES       | 空值        |
| 在工作室中查看管道运行详细信息                                        | GA               | YES       | 空值        |
| 在 Azure ML 设计器中创建、运行、可视化和发布管道          | 公共预览版   | YES       | 空值        |
| Azure Databricks 与 ML 管道集成                             | GA               | 是        | 空值        |
| 在 Azure ML 设计器中创建管道终结点                             | 公共预览版   | YES       | 空值        |
| **集成笔记本** |   | | |
| 工作区笔记本和文件共享                                        | GA               | YES       | 空值        |
| R 和 Python 支持                                                       | GA               | YES       | 空值        |
| 虚拟网络支持                                                    | 公共预览版   | 是        | 空值        |
| **计算实例** |    | | |
| 集成笔记本的托管计算实例                         | GA               | 是        | 空值        |
| Jupyter、JupyterLab 集成                                            | GA               | YES       | 空值        |
| 虚拟网络 (VNet) 支持                                             | 公共预览版   | YES       | 空值        |
| **SDK 支持** |    | | |
| R SDK 支持                                                              | 公共预览版   | YES       | 空值        |
| Python SDK 支持                                                         | GA               | YES       | 空值        |
| **安全性** |   | | |
| 虚拟网络 (VNet) 支持培训                                | GA               | YES       | 空值        |
| 虚拟网络 (VNet) 支持推理                               | GA               | YES       | 空值        |
| 对终结点身份验证评分                                            | 公共预览版   | YES       | 空值        |
| 工作区专用链接                                                     | 公共预览版   | 是        | 空值        |
| VNet 后的 ACI                                                            | 公共预览版   | 是        | 空值        |
| VNet 后的 ACR                                                            | 公共预览版   | 是        | 空值        |
| AKS 群集的专用 IP                                                  | 公共预览版   | 是        | 空值        |
| **计算** |   | | |
| 跨工作区的配额管理                                         | GA               | YES       | 空值        |
| **用于机器学习的数据** | | | |
| 从 SDK 创建、查看或编辑数据集和数据存储                  | GA               | YES       | 空值        |
| 从 UI 创建、查看或编辑数据集和数据存储                   | GA               | YES       | 空值        |
| 查看、编辑或删除 SDK 中的数据集偏移监视器                   | 公共预览版   | YES       | 空值        |
| 查看、编辑或删除 UI 中的数据集偏移监视器                    | 公共预览版   | YES       | 空值        |
| **机器学习生命周期** |    | | |
| 模型分析                                                            | GA               | PARTIAL   | 空值        |
| 适用于机器学习的 Azure DevOps 扩展 & Azure ML CLI         | GA               | YES       | 空值        |
| 基于 FPGA 的硬件加速模型                                     | GA               | 是        | 空值        |
| Visual Studio Code 集成                                             | 公共预览版   | 是        | 空值        |
| 事件网格集成                                                     | 公共预览版   | YES       | 空值        |
| 将 Azure 流分析与 Azure 机器学习集成               | 公共预览版   | 是        | 空值        |
| **标记** |    | | |
| 标记项目管理门户                                        | GA               | YES       | 空值        |
| Labeler 门户                                                            | GA               | YES       | 空值        |
| 使用专用劳动力标签                                          | GA               | YES       | 空值        |
| ML 辅助标签 (图像分类和对象检测)            | 公共预览版   | YES       | 空值        |
| **负责的 ML** |    | | |
| UI 的可说明性                                                       | 公共预览版   | 是        | 空值        |
| 差异隐私 WhiteNoise 工具包                                    | OS              | 是        | 空值        |
| 用于实现数据表的 Azure 机器学习中的自定义标记              | GA               | 是        | 空值        |
| 公平 AzureML 集成                                               | 公共预览版   | 是        | 空值        |
| Interpretability SDK                                                      | GA               | YES       | 空值        |
| **培训** |    | | |
| 试验日志流式处理                                              | GA               | YES       | 空值        |
| 强化学习                                                     | 公共预览版   | 是        | 空值        |
| 试验 UI                                                         | GA               | YES       | 空值        |
| .NET integration ML.NET 1。0                                                | GA               | YES       | 空值        |
| **推理** |   | | |
| 批处理推理                                                          | GA               | YES       | 空值        |
| Data Box Edge 与 FPGA                                                    | 公共预览版   | 是        | 空值        |
| **其他** |    | | |
| 开放数据集                                                              | 公共预览版   | YES       | 空值        |
| 自定义认知搜索                                                    | 公共预览版   | YES       | 空值        |
| 许多模型                                                                | 公共预览版   | 是        | 空值        |



### <a name="additional-azure-china-limitations"></a>其他 Azure 中国限制

* Azure 中国拥有有限的 VM SKU，特别是对于 GPU SKU。 它只有 NCv3 系列 (V100) 。
* REST API 终结点不同于全局 Azure。 使用下表查找 Azure 中国区的 REST API 终结点：

    | REST 终结点                 | 全球 Azure                                 | 中国-政府                           |
    |------------------|--------------------------------------------|--------------------------------------------|
    | 管理平面 | `https://management.azure.com/`              | `https://management.chinacloudapi.cn/`       |
    | 数据平面       | `https://{location}.experiments.azureml.net` | `https://{location}.experiments.ml.azure.cn` |
    | Azure Active Directory              | `https://login.microsoftonline.com`          | `https://login.chinacloudapi.cn`             |

* 如果需要访问公共数据，示例笔记本可能不起作用。
* IP 地址范围： [VNet 强制隧道](how-to-secure-training-vnet.md#forced-tunneling) 说明中使用的 CLI 命令不返回 IP 范围。 改用 [适用于 Azure 中国的 AZURE IP 范围和服务标记](https://www.microsoft.com//download/details.aspx?id=57062) 。
* 目前启用了 "私有" 链接的工作区中不支持 Azure 机器学习计算实例预览，但在下一次将服务扩展部署到所有 AML 区域时，将支持 CI。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 机器学习可用于的区域的详细信息，请参阅 [产品（按区域](https://azure.microsoft.com/global-infrastructure/services/)）。
