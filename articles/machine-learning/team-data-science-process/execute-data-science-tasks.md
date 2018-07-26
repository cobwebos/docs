---
title: 执行数据科学任务 - Azure 机器学习 | Microsoft Docs
description: 数据科学家如何以可跟踪的协作型版本控制方式执行数据科学项目。
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: deguhath
ms.openlocfilehash: 19267429510207129b0229dc55fbd46f12977d5d
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39112638"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>执行数据科学任务：探索、建模和部署

典型的数据科学任务包括数据探索、建模和部署。 本文介绍如何使用**交互式数据探索、分析和报告 (IDEAR)** 与**自动建模和报告 (AMAR)** 实用工具来完成多个常见数据科学任务，例如交互式数据探索、数据分析、报告和建模。 此外，还概述了用于通过如下所述的各种工具包和数据平台在生产环境中部署模型的选项：

- [Azure 机器学习](../service/index.yml)
- [包含机器学习服务的 SQL-Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-services#in-database-analytics-with-sql-server)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)


## 1.<a name='DataQualityReportUtility-1'></a>浏览 

数据科学家可通过各种方法执行探索和报告：使用适用于 Python 的库和包（例如 matplotlib）或使用 R（例如 ggplot 或 lattice）。 数据科学家可以自定义此类代码，以满足特定方案中数据探索的需要。 结构化数据与非结构化数据（例如文本或图像）的处理需求有所不同。 

Azure Machine Learning Workbench 等产品还提供了[高级数据准备](../desktop-workbench/tutorial-bikeshare-dataprep.md)用于数据整理和探索，包括特征创建。 用户应确定哪种工具、库和包最适合其需要。 

完成此阶段后，交付件是一份数据探索报告。 该报告应提供相当全面的数据视图用于建模，并评估这些数据是否适合继续在建模步骤中使用。 以下部分所述的，用于半自动化探索、建模和报告的 Team Data Science Process (TDSP) 实用工具也提供标准化的数据探索和建模报告。 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>使用 IDEAR 实用工具进行交互式数据探索、分析和报告

这款基于 R Markdown 或基于 Python Notebook 的实用工具提供一个灵活的交互式工具用于评估和探索数据集。 用户可以最少的编码工作从数据集快速生成报告。 用户可以单击按钮，将交互式工具中的探索结果导出到最终报告，并可将此报告传递到客户端或者使用它来决定在后续的建模步骤中包含哪些变量。

目前，此工具仅适用于内存中的数据帧。 需要使用 YAML 文件指定要探索的数据集的参数。 有关详细信息，请参阅 [TDSP 数据科学实用程序中的 IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils)。


## 2.<a name='ModelingUtility-2'></a>建模

有大量的工具包和程序包可用于训练各种语言的模型。 只要符合相关业务用例和生产方案的性能、准确度和延迟要求，数据科学家就可以任意使用他们顺手的工具。

下一部分介绍如何使用基于 R 的 TDSP 实用工具进行半自动化建模。 可以使用此 AMAR 实用工具快速生成基线模型，以及为了提供性能更佳的模型而要优化的参数。
以下模型管理部分介绍如何配置一个用于注册和管理多个模型的系统。


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>模型训练：使用 AMAR 实用工具执行建模和报告

[自动建模和报告 (AMAR) 实用工具](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling)提供一个可自定义的半自动化工具，它可以通过超参数扫描执行建模并比较这些模型的准确性。 

该建模实用工具是一个 R Markdown 文件，运行该文件可生成自包含性 HTML 输出，其中包含一个目录，用于方便浏览其不同的节。 运行（编织）此 Markdown 文件时会执行三个算法：正则化回归（使用 glmnet 包）、随机林（使用 randomForest 包）和提升树（使用 xgboost 包）。 上述每个算法均会生成一个定型的模型。 然后会比较这些模型的准确性，并报告相对特征重要性绘图。 目前，有两个实用程序：一个用于二进制分类任务，另一个用于回归任务。 二者之间的主要区别在于指定这些学习任务的控制参数和准确性指标的方式。 

YAML 文件用于指定：

- 数据输入（SQL 源或 R-Data 文件） 
- 使用数据的哪些部分进行定型及使用哪些部分进行测试
- 要运行哪些算法 
- 用于优化模型的控制参数选项：
    - 交叉验证 
    - 启动
    - 交叉验证折叠数
- 每个算法的超参数集。 

也可在 Yaml 文件中修改要扫描的算法数量、用于优化的折叠数、超参数和超参数集的数量，以快速运行模型。 例如，可使用较低的 CV 折叠数、较低的参数集数运行它们。 如有必要，也可以使用较高的 CV 折叠数或较大的参数集数以更全面的方式运行它们。

有关详细信息，请参阅 [TDSP 数据科学实用程序中的自动建模和报告实用工具](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling)。

### <a name="model-management"></a>模型管理
生成多个模型后，往往需要配置一个系统来注册和管理这些模型。 通常，需要创建脚本或 API 的组合，以及后端数据库或版本控制系统。 可考虑用于这些管理任务的几个选项包括：

1. [Azure 机器学习 - 模型管理服务](../service/index.yml)
2. [MIT 提供的 ModelDB](https://mitdbg.github.io/modeldb/) 
3. [用作模型管理系统的 SQL-Server](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

## 3.<a name='Deployment-3'></a>部署

生产部署可让模型在企业中发挥积极作用。 所部署模型提供的预测可用于业务决策。

### <a name="production-platforms"></a>生产平台
可通过多种方法和平台将模型投入生产。 下面是几个选项：


- [Azure 机器学习中的模型部署](../desktop-workbench/model-management-overview.md)
- [SQL-Server 中的模型部署](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> 在部署之前，必须确保模型的延迟评分够低，使模型可在生产环境中使用。
>
>

演练中提供的其他示例演示了适用于**具体方案**的操作过程的所有步骤。 [示例演练](walkthroughs.md)一文列出了相关步骤并以缩略图说明的形式提供了链接。 这些演练演示如何将云、本地工具和服务合并到工作流或管道中，以创建智能应用程序。

> [!NOTE]
> 对于使用 Azure 机器学习工作室的部署，请参阅[部署 Azure 机器学习 Web 服务](../studio/publish-a-machine-learning-web-service.md)。
>
>

### <a name="ab-testing"></a>A/B 测试
如果在生产环境中部署了多个模型，执行 [A/B 测试](https://en.wikipedia.org/wiki/A/B_testing)来比较模型的性能可能很有用。 

 
## <a name="next-steps"></a>后续步骤

[跟踪数据科学项目的进度](track-progress.md)介绍了数据科学家可通过哪些方式跟踪数据科学项目的进度。

[模型操作和 CI/CD](ci-cd-flask.md) 展示如何使用开发模型执行 CI/CD。


