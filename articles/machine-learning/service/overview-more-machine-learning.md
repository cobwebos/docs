---
title: Microsoft 机器学习产品选项的比较 - Azure | Microsoft Docs
description: 比较 Microsoft 提供的用于生成、部署和管理机器学习模型的各种产品。 确定要为解决方案选择哪种产品。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: garyericson
ms.author: garye
ms.date: 09/24/2018
ms.openlocfilehash: 182504373795b3cb0f2794acbed5e253ac6bc95c
ms.sourcegitcommit: 3150596c9d4a53d3650cc9254c107871ae0aab88
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2018
ms.locfileid: "47419553"
---
# <a name="what-are-the-machine-learning-product-options-from-microsoft"></a>Microsoft 提供哪些机器学习产品选项？

Microsoft 提供多种多样的产品选项用于生成、部署和管理机器学习模型。 本文将比较这些产品，并帮助你选择所需的产品，以便最有效地开发机器学习解决方案。

| 机器学习产品 | 描述 | 作用 |
|-|-|-|
| 在云中 | | |
| [Azure 机器学习服务](#azure-machine-learning-services) | 适用于机器学习的托管云服务  | 使用 Python 和 CLI 在 Azure 中训练、部署和管理模型 |
| [Azure 机器学习工作室](#azure-machine-learning-studio) | 机器学习的拖放式可视界面 | 使用预配置的算法生成、试验和部署模型 |
| [Azure Databricks](#azure-databricks) | 基于 Spark 的分析平台 | 生成和部署模型与数据工作流 |
| [Azure 认知服务](#azure-cognitive-services) | 包含预生成 AI 和机器学习模型的 Azure 服务 | 轻松将智能功能轻松添加到应用中 |
| [Azure Data Science Virtual Machine](#azure-data-science-virtual-machine) | 预装了数据科学工具的虚拟机 | 在预配置的环境中开发机器学习解决方案 |
| 本地 | | |
| [SQL Server 机器学习服务](#sql-server-machine-learning-services) | SQL 中嵌入的分析引擎 | 在 SQL Server 内部生成和部署模型 |
| [Microsoft Machine Learning Server](#microsoft-machine-learning-server) | 用于预测分析的独立企业服务器 | 使用 R 和 Python 生成与部署模型 |
| 开发人员工具 | | |
| [ML.NET](#mlnet) | 开源跨平台机器学习 SDK | 开发适用于 .NET 应用程序的机器学习解决方案 |
| [Windows ML](#windows-ml) | Windows 10 机器学习平台 | 在 Windows 10 设备上评估已训练的模型 |

## <a name="azure-machine-learning-service"></a>Azure 机器学习服务

[Azure 机器学习服务](overview-what-is-azure-ml.md)（预览版）是用于大规模训练、部署和管理机器学习模型的完全托管式云服务。 它完全支持开源技术，可让你使用数以万计的开源 Python 包，例如 TensorFlow、PyTorch 和 scikit-learn。 它还提供丰富的工具，例如 [Azure Notebook](https://notebooks.azure.com/)、[Jupyter Notebook](http://jupyter.org) 或 [Visual Studio Code Tools for AI](https://visualstudio.microsoft.com/downloads/ai-tools-vscode/)，以方便探索和转换数据，然后训练和部署模型。 Azure 机器学习服务包含可以轻松、高效和准确地自动化模型生成和优化的功能。

在 Azure 机器学习服务中可以使用 Python 和 CLI 以云的规模训练、部署和管理机器学习模型。

>[!Note]
> 可以免费尝试 Azure 机器学习。 不需要信用卡或 Azure 订阅。 立即开始。 https://azure.microsoft.com/free/

## <a name="azure-machine-learning-studio"></a>Azure 机器学习工作室

[Azure 机器学习工作室](../studio/what-is-ml-studio.md)提供交互式的可视工作区，用于通过预生成的机器学习算法快速轻松地生成、测试和部署模型。 机器学习工作室将模型发布为可让自定义应用或 BI 工具（如 Excel）方便使用的 Web 服务。
无需任何编程 - 在交互式画布上连接数据集和分析模块，然后按几个鼠标将其部署，即可构造机器学习模型。

希望在不编写任何代码的情况下开发和部署模型时，可以使用机器学习工作室。

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="azure-databricks"></a>Azure Databricks

[Azure Databricks](/azure/azure-databricks/what-is-azure-databricks) 是基于 Apache Spark 的分析平台，已针对 Microsoft Azure 云服务平台进行优化。 Databricks 与 Azure 集成，以提供一键式安装程序、简化的工作流程以及交互式工作区，从而使数据科学家、数据工程师和业务分析员之间可以进行协作。
在基于 Web 的 Notebook 中使用 Python、R、Scala 和 SQL 代码可以查询、可视化数据以及为其建模。

想要在 Apache Spark 中协作生成机器学习解决方案时，可以使用 Databricks。

## <a name="azure-cognitive-services"></a>Azure 认知服务

[Azure 认知服务](/azure/cognitive-services/welcome)是一组 API，可用于生成使用自然通信方法的应用。 借助这些 API，只需编写几行代码，就能让应用看到、听到、讲出、理解和解释用户的需求。 将智能功能轻松添加到应用，例如： 

- 情感和观点检测
- 视觉和语音识别
- 语言理解 (LUIS)
- 知识和搜索

使用认知服务可以开发跨设备和平台的应用。 API 不断改进，且易于设置。

## <a name="azure-data-science-virtual-machine"></a>Azure 数据科学虚拟机

[Azure Data Science Virtual Machine](../data-science-virtual-machine/overview.md) 是专为开展数据科学生成的 Microsoft Azure 云上的自定义虚拟机环境。 它预装并预配了许多热门数据科学和其他工具，可为高级分析快速生成智能应用程序。
Data Science Virtual Machine 提供适用于 Windows 和 Linux Ubuntu 的版本（Linux CentOS 不支持 Azure 机器学习服务）。
有关特定版本的信息以及版本功能的列表，请参阅 [Azure Data Science Virtual Machine 简介](../data-science-virtual-machine/overview.md)。
支持将 Data Science Virtual Machine 用作 Azure 机器学习服务的目标。

需要在单个节点上运行或托管作业时，可以使用数据科学 VM。 或者，需要在单个计算机上远程提高处理能力时，也可以使用它。

## <a name="sql-server-machine-learning-services"></a>SQL Server 机器学习服务

[SQL Server Microsoft 机器学习服务](https://docs.microsoft.com/sql/advanced-analytics/r/r-services)为 SQL Server 数据库中的关系数据添加了采用 R 和 Python 语言的统计分析、数据可视化和预测分析。 Microsoft 提供的 R 和 Python 库包括高级建模与机器学习算法，可在 SQL Server 中大规模并行运行。

需要对 SQL Server 中的关系数据使用内置 AI 和预测分析时，可以使用 SQL Server 机器学习服务。

## <a name="microsoft-machine-learning-server"></a>Microsoft 机器学习服务器

[Microsoft 机器学习服务器](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)是一个企业服务器，用于托管和管理 R 与 Python 进程的并行与分布式工作负荷。 Microsoft Machine Learning Server 在 Linux、Windows、Hadoop 和 Apache Spark 上运行，也适用于 [HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/)。 它为使用 [RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)、[revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package) 和 [MicrosoftML 包](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)生成的解决方案提供执行引擎，并扩展了开源 R 和 Python，支持高性能分析、统计分析、机器学习和巨型数据集。 此功能通过可以连同服务器一起安装的专属包提供。 对于开发，可以使用[针对 Visual Studio 的 R 工具](https://www.visualstudio.com/vs/rtvs/)和[针对 Visual Studio 的 Python 工具](https://www.visualstudio.com/vs/python/)等 IDE。

需要在服务器上使用 R 和 Python 生成与操作化模型时，或者需要在 Hadoop 或 Spark 群集上大规模分配 R 和 Python 训练工作负荷时，可以使用 Microsoft Machine Learning Server。

## <a name="mlnet"></a>ML.NET

[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/) 是免费的开源跨平台机器学习框架，可用于生成自定义机器学习解决方案并将其集成到 .NET 应用程序中。

想要将机器学习解决方案集成到 .NET 应用程序时，可以使用 ML.NET。

## <a name="windows-ml"></a>Windows ML

[Windows ML](https://docs.microsoft.com/windows/uwp/machine-learning/) 可让你使用应用程序中已训练的机器学习模型，在 Windows 10 设备本地评估训练的模型。

想要在 Windows 应用程序中使用训练的机器学习模型时，可以使用 Windows ML。

## <a name="next-steps"></a>后续步骤

- 若要了解 Microsoft 提供的所有人工智能 (AI) 开发产品，请参阅 [Microsoft AI 平台](https://www.microsoft.com/ai)
- 有关如何开发 AI 解决方案的培训内容，请参阅 [Microsoft AI 学校](https://aischool.microsoft.com/learning-paths)
