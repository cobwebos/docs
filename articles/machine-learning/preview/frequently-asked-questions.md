---
title: "Azure 机器学习 2017 预览版常见问题解答 | Microsoft Docs"
description: "本文包含 Azure 机器学习预览功能的常见问题和解答"
services: machine-learning
author: serinakaye
ms.author: serinak
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 08/30/2017
ms.openlocfilehash: 33583fb3d9bca94592c05c4c39c802e673a2ea8a
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2017
---
# <a name="azure-machine-learning-frequently-asked-questions"></a>Azure 机器学习常见问题

## <a name="general-product-questions"></a>一般产品问题

**什么是 Azure 机器学习？**

Azure 机器学习是一种完全托管的 Azure 服务，可供你创建、测试、管理及部署机器学习和 AI 模型。 我们的新服务和可下载的应用程序可提供一种代码优先方法，该方法利用云、本地和边缘来提供兼具功效、速度和灵活性的培训模型、部署模型、管理模型和监视模型。 此外，Azure 机器学习工作室还可提供基于浏览器的可视化拖放编写环境，无需进行任何编码。 

**如何开始使用预览版？**

在 Azure 中注册试验服务是最简单的开始方法。 通过该门户，还可以下载免费的工作台应用程序，或者注册模型管理帐户。 此外，还可以访问我们的文档站点，获取操作方法文章、视频、演示代码等。 

**我没有 Azure 订阅。是否仍可以试用新服务？**

作为 Microsoft Azure 产品组合的一部分，我们的新服务需要 Azure 订阅。 此外，必须具有足够权限，才能创建诸如资源组、虚拟机等资产。 

**哪些区域提供新服务？**

在[按区域划分的 Azure 产品](https://azure.microsoft.com/regions/services/)页上的 Azure 机器学习下，可找到试验和模型管理服务的受支持 Azure 区域。

随着产品的不断开发，将会新增更多区域。  在我们的 [Azure 机器学习反馈](https://feedback.azure.com/forums/257792-machine-learning)站点上，可以帮助我们确定要部署区域的优先顺序。 

**需要哪些其他 Azure 服务？**

为了充分体验 Azure 机器学习的全部功能，Azure 机器学习将使用 Visual Studio VSTS 帐户、Azure Blob 存储、Azure 容器注册表、数据科学 VM 或 HDInsight 计算以及 Azure 容器服务。

**Azure 机器学习如何与 SQL Server 2017 中的 Microsoft 机器学习服务相关联？**   

SQL Server 2017 中的机器学习服务是一个可扩展的可缩放平台，可用于将机器学习任务集成到数据库工作流。 非常适合需要本地解决方案的情况，例如数据移动成本高昂或不可维持。 相较之下，云或混合工作负载非常适合我们新的 Azure 服务。 

**是否支持 Python 和 R？其他编程语言呢，例如 C++**

目前仅支持 Python。 我们正在进行 R 集成方面的工作，预计不久即可完成。 

**Azure 机器学习如何与适用于 Spark 的 Microsoft 机器学习相关联？**

答：MMLSpark 可为 Apache Spark 提供深度学习和数据科学工具，侧重于工作效率、简化试验和先进算法。 MMLSpark 将 Spark 机器学习管道与 Microsoft 认知工具包和 OpenCV 相集成。 可为映像和文本数据创建功能强大、高度可缩放的预测模型和分析模型。 MMLSpark 基于开源许可证提供，作为一组可使用模型和算法包含在 AML Workbench 中。 有关 MMLSpark 的详细信息，请访问我们的产品文档。 

**新工具和服务支持哪些版本的 Spark？顶部**

Workbench 当前包括并支持与 Apache Spark 2.1 兼容的 MMLSpark 版本 0.8。 还可以选择在 Linux 虚拟机上使用 MMLSpark 0.8 的支持 GPU 的 Docker 映像。

## <a name="experimentation-service"></a>试验服务

**什么是 Azure 机器学习试验服务？**

试验服务是一种托管型 Azure 服务，该服务将机器学习试验提升到新的水平。 可以在本地或者在云中生成试验。 在桌面上快速生成原型，然后扩展到虚拟机或 Spark 群集。 采用最新 GPU 技术的 Azure VM 使你能够快速又有效地参与深度学习。 我们还包含了与 Git 的深度集成，因此可以轻松地中途加入现有工作流，从而执行代码跟踪、配置和协作。 

**试验服务如何收费？**

与 Azure 机器学习试验服务相关联的前两名用户免费。 会向其他用户公共预览版收取费用，每月 50 美元。 有关定价和计费的详细信息，请访问我们的定价页。

**是否将根据运行的试验数量来收费？**

不会。凭借试验服务，可以运行大量所需的试验，并且仅根据用户的数量进行收费。 试验计算资源将单独收费。 我们建议你执行多次试验，以便找到最适合自己的解决方案的模型。   

**可以使用哪些特定类型的计算和存储资源？**

试验服务可以在以下资源上执行试验：本地计算机（直接执行或基于 Docker 执行）、[Azure 虚拟机](https://azure.microsoft.com/services/virtual-machines/)和 [HDInsight](https://azure.microsoft.com/services/hdinsight/)。 该服务还可访问 [Azure 存储](https://azure.microsoft.com/services/storage/)帐户以存储执行输出，并可利用 [Visual Studio 团队服务](https://azure.microsoft.com/services/visual-studio-team-services/)帐户执行版本控制和 Git 存储。 请注意，对于任何使用的计算和存储资源，将根据这些资源各自的定价单独收费。  


## <a name="model-management"></a>模型管理

**什么是 Azure 机器学习模型管理？**

Azure 机器学习模型管理是一种托管型 Azure 服务，可供数据科学家和开发运维团队将预测模型可靠地部署到各种环境中。 Git 存储库和 Docker 容器提供可跟踪性和可重复性。 可在云、本地或边缘中可靠地部署模型。 投入生产后，可以管理模型性能，如果性能出现下降，可主动进行重新训练。 可以将本地计算机上的模型部署到 [Azure VM](https://azure.microsoft.com/services/virtual-machines/)、[HDInsight](https://azure.microsoft.com/services/hdinsight/) 上的 Spark 或 Kubernetes 安排的 [Azure 容器服务](https://azure.microsoft.com/services/container-service/)群集。  

**什么是“模型”？**

模型是针对模型管理得到提升的试验运行的输出。 在托管帐户中注册的模型会根据计划进行计数，包括通过重新训练或版本迭代更新的模型。

**什么是“托管模型”？**

模型是培训流程的输出，并且是针对培训数据进行的机器学习算法应用。 借助模型管理，可以作为 Web 服务部署模型、管理模型的各个版本以及监视模型的性能和指标。 “托管”模型已向 Azure 机器学习模型管理帐户注册。 例如，以尝试预测销售的方案为例。 在试验阶段，通过使用不同的数据集或算法生成了许多模型。 如果已生成四个模型，但每个模型的准确性都不同，可以选择仅注册准确性最高的模型。 已注册的模型成为你的第一个托管模型。
 
**什么是“部署”？**

借助模型管理，可以在 Azure 中以打包的 Web 服务容器的形式部署模型。 可以使用 REST API 调用这些 Web 服务。 每个 Web 服务计为一个部署，活动部署的总数会计入计划。 以销售预测为例，在部署性能最佳的模型后，计划就会递增一个部署。 之后，如果重新训练并部署另一个版本，你将拥有两个部署。 如果确定较新的模型更好，并删除原始模型，则部署计数随之递减一个。  

**哪些特定计算资源可用于我的部署？** 

模型管理可以采用以下形式运行部署：作为已注册到 [Azure 容器服务](https://azure.microsoft.com/services/container-service/)的 Docker 容器、作为 [Azure 虚拟机](https://azure.microsoft.com/services/virtual-machines/)或在本地计算机上。 不久会添加其他部署目标。 请注意，对于任何使用的计算资源，将根据这些资源各自的定价单独收费。     

**是否可以使用 Azure 机器学习模型管理来部署使用试验服务以外的工具创建的模型？**

在部署完使用试验服务创建的模型后，可获得最佳体验。 还可以部署使用其他框架和工具创建的模型。 我们支持多种模型，包括 MMLSpark、TensorFlow、Microsoft 认知工具包、scikit-learn、Keras 等。 

**是否可以使用自己的 Azure 资源？**

可以。试验服务和模型管理可与多个 Azure 数据存储、计算工作负载和其他服务结合使用。 有关所需 Azure 服务的更多详细信息，请参阅我们的技术文档。

**是否支持本地和云部署方案？**

是的。 我们通过 Docker 容器支持本地和云部署方案。 本地执行目标包括：单节点 Docker 部署、[带有 ML 服务的 Microsoft SQL Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-services)、Hadoop 或 Spark。 我们还通过 Docker 支持云部署，包括：通过 Azure 容器服务及 Kubernetes、HDInsight 或 Spark 群集实现群集部署。 通过 Docker 容器和 Azure IOT Edge 支持边缘方案。 

**是否可以在另一台主机上运行使用 Azure 机器学习 CLI 创建的 Docker 映像？**

是的。 可以在任何 Docker 主机上将映像用作 Web 服务，前提是该主机的计算资源足以承载 Docker 映像。

**是否支持已部署模型的重新训练？**

是，可以部署同一模型的多个版本。 模型管理可对所有已更新的模型和映像提供服务更新支持。

## <a name="workbench"></a>工作台

**什么是 Azure Machine Learning Workbench？**

Azure Machine Learning Workbench 是为专业数据科学家打造的助手应用程序。 Machine Learning Workbench 适用于 Windows 和 Mac，可为机器学习解决方案提供概述、管理和控制。 Machine Learning Workbench 包含对 Microsoft 和开源社区中的前沿 AI 框架的访问权限。 我们引入了最常用的数据科学工具包，包括 TensorFlow、Microsoft 认知工具包、Spark ML、scikit-learn 等。 我们还支持与常用数据科学 IDE 集成，例如 Jupyter Notebook、PyCharm 和 Visual Studio Code。 Machine Learning Workbench 具有内置的数据准备功能，可快速取样、了解和准备数据，不论数据是否已结构化。 我们新的数据准备工具称为 [PROSE](https://microsoft.github.io/prose/)，它基于 Microsoft Research 的前沿技术构建。  

**Workbench 是否是一个 IDE？**

不能。 Machine Learning Workbench 设计成为诸如 Jupyter Notebook、Visual Studio Code 和 PyCharm 等常用 IDE 的助手，并非是一个具备完整功能的 IDE。 Machine Learning Workbench 可提供一些基本的文本编辑功能，但并不支持调试、智能感知和其他常用 IDE 功能。 我们建议你使用最喜欢的 IDE 来进行代码开发、编辑和调试。 

**是否要为使用 Azure Machine Learning Workbench 付费？**

不能。 Azure Machine Learning Workbench 是一个免费的应用程序。 可以在所需的多台计算机上为多位用户下载它。 若要使用 Azure Machine Learning Workbench，必须具有试验帐户。 。  

**是否支持命令行功能？**

答：是，Azure 机器学习提供了一个完整的 CLI 接口。 Machine Learning CLI 默认随 Azure Machine Learning Workbench 一同安装。 它还可在 Azure 上作为 Linux 数据科学虚拟机的一部分提供，将集成到 [Azure CLI](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest) 中


**是否可以通过 Workbench 使用 Jupyter Notebook？**

能！ 可以在 Workbench 中运行 Jupyter Notebook，Workbench 可作为承载应用程序的客户端，就像将浏览器用作客户端一样。 

**支持哪些 Jupyter Notebook 内核？**

答：Workbench 随附的当前版本的 Jupyter 可为 aml_config 文件夹中的每个“runconfig”文件启动一个 Python 3 内核和一个附加内核。 支持的配置包括：
- 本地 Python
- 本地或远程 Docker 中的 Python

## <a name="data-formats-and-capabilities"></a>数据格式和功能

**Workbench 中的数据引入当前支持哪些文件格式？**

Workbench 中的数据准备工具当前支持以下格式的引入： 
- 分隔文件，如 CSV、TSV 等。  
- 宽度固定文件
- 纯文本文件
- Excel (.xls/xlsx)
- JSON 文件
- Parquet 文件 
- 自定义文件（脚本） 如果解决方案需要来自其他源的数据引入，则 Python 代码可用于... 

**当前支持哪些数据存储位置？**

对于公共预览版，Workbench 支持来自以下来源的数据引入： 
- 本地硬盘驱动器或映射的网络存储位置
- Azure BLOB 或 Azure 存储（需要 Azure 订阅）
- Azure SQL Server
- Microsoft SQL Server


**哪些类型的数据整理、准备和转换可用？**

对于公共预览版，Workbench 支持“按示例派生列”、“按示例拆分列”、“文本群集”、“处理缺失值”等。  Workbench 还支持数据类型转换、数据聚合（COUNT、MEAN、VARIANCE 等）和复杂数据联接。 有关支持的功能的完整列表，请访问我们的产品文档。 

**Azure Machine Learning Workbench、试验或模型管理是否会对数据大小强制执行限制？**

答：不会，新的服务不会实施任何数据限制。 不过，执行数据准备、模型训练、试验或部署所在的环境会引入限制。 例如，如果面向本地环境进行训练，会受到硬盘驱动器内可用空间的限制。 此外，如果面向 HDInsight，会受到任何关联尺寸或计算约束的限制。 

## <a name="algorithms-and-libraries"></a>算法和库

**Azure Machine Learning Workbench 支持哪些算法？**

我们的预览版产品和服务包含开源社区的精选内容。 我们支持多种算法和库，包括 TensorFlow、scikit-learn、Apache Spark 和 Microsoft 认知工具包。 Azure Machine Learning Workbench 还打包了 [Microsoft revoscalepy](https://docs.microsoft.com/sql/advanced-analytics/python/what-is-revoscalepy) 程序包。

**Azure 机器学习如何与 Microsoft 认知工具包相关联？**

[Microsoft 认知工具包](https://www.microsoft.com/cognitive-toolkit/)是我们的新工具和服务支持的众多框架之一。 认知工具包是统一的深度学习工具包，可供使用和组合常用的机器学习模型，包括前馈深层神经网络、卷积网络、序列到序列和循环网络。 有关 Microsoft 认知工具包的详细信息，请访问我们的[产品文档](https://docs.microsoft.com/cognitive-toolkit/)。 

## <a name="pricing-and-billing"></a>定价和计费

**在预览期间，是否需要为 Azure 机器学习付费？** 

答：Azure Machine Learning Workbench 应用程序免费提供给 Azure 订阅者。 试验服务和模型管理提供免费层以及付费层，在使用公共预览版期间可按折扣价购买付费层。

**是否将根据运行的试验数量来收费？**

否。借助试验服务，可以根据需要运行任意数目的试验，仅根据用户数量进行收费。 试验计算资源将单独收费。  我们建议你执行多次试验，以便找到最适合自己的解决方案的模型。 

**是否将根据调用 Web 服务的次数进行收费？**

不能。 可以随时按需调用 Web 服务，不会产生任何的模型管理费用。 可以完全控制部署的缩放以满足应用程序需求。

**如何在 Azure 机器学习模型管理中对已购买单位的数量进行缩放？**

可以使用 Azure 门户或 CLI 向上或向下更改单位数量。 

**将如何进行计费？**

帐单将按日生成。 为方便计费，一天从午夜 UTC 算起。 帐单将按月生成。 与 Azure 机器学习一起使用的任何 Azure 服务都会单独计费。 费用可能包括但不限于： 
- 计算费用
- HDInsight
- Azure 容器服务
- Azure 容器注册表 
- Azure Blob 存储
- Application Insights
- Azure 密钥保管库
- Visual Studio Team Services
- Azure 事件中心
- Azure 流分析 有关更多详细信息，或想要查看示例帐单，请访问我们的定价页。 

## <a name="support-and-training"></a>支持和培训

**在哪里可以获得 Azure 机器学习的培训？**

[Azure 机器学习文档中心](./overview-what-is-azure-ml.md)提供视频教程和操作指南。 这些分步指南提供服务介绍，并对数据科学生命周期进行了说明。 我们会持续更新机器学习中心的内容。 可以在用户反馈论坛中提交有关其他学习材料的请求。

**如何获取 Azure 机器学习的支持？**

有关技术支持，请转到 Azure 支持，然后选择“机器学习”。 Azure 机器学习还在 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-machine-learning) 上设有一个活动社区，标记为“Azure-Machine-Learning”，由团队监视。  可以在 [Azure 机器学习反馈](https://feedback.azure.com/forums/257792-machine-learning)站点上提交建议和功能请求。 
