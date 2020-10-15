---
title: 教程：使用 Azure 机器学习创建和部署自定义技能
titleSuffix: Azure Cognitive Search
description: 本教程演示如何使用 Azure 机器学习为 Azure 认知搜索的 AI 扩充管道构建和部署自定义技能。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: fa961a5a6d3a3b827a082fbac2acc3431ac40949
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057597"
---
# <a name="tutorial-build-and-deploy-a-custom-skill-with-azure-machine-learning"></a>教程：使用 Azure 机器学习构建和部署自定义技能 

在本教程中，你将使用[酒店评论数据集](https://www.kaggle.com/datafiniti/hotel-reviews)（在 Creative Commons 许可证 [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/legalcode.txt) 下分发）通过 Azure 机器学习创建[自定义技能](./cognitive-search-aml-skill.md)，以从评论中提取基于方面的情绪。 这样一来，就可以将同一评论中的正面和负面情绪正确归因于已标识的实体，例如员工、客房、大厅或游泳池。

为了在 Azure 机器学习中训练基于方面的情绪模型，你将使用 [nlp recipes 存储库](https://github.com/microsoft/nlp-recipes/tree/master/examples/sentiment_analysis/absa)。 然后，你会将该模型部署为 Azure Kubernetes 群集上的终结点。 部署后，该终结点将作为 AML 技能添加到扩充管道中，供认知搜索服务使用。

提供了两个数据集。 如果希望自己训练模型，你需要 hotel_reviews_1000.csv 文件。 希望跳过训练步骤？ 下载 hotel_reviews_100.csv 文件。

> [!div class="checklist"]
> * 创建 Azure 认知搜索实例
> * 创建 Azure 机器学习工作区（搜索服务和工作区应在同一订阅中）
> * 训练模型并将其部署到 Azure Kubernetes 群集
> * 将 AI 扩充管道链接到已部署的模型
> * 从已部署的模型引入输出作为自定义技能

> [!IMPORTANT] 
> 此技能以公共预览版提供。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 目前不支持 .NET SDK。

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - 获取[免费订阅](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [认知搜索服务](./search-get-started-arm.md)
* [认知服务资源](../cognitive-services/cognitive-services-apis-create-account.md?tabs=multiservice%2cwindows)
* [Azure 存储帐户](../storage/common/storage-account-create.md?tabs=azure-portal&toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Azure 机器学习工作区](../machine-learning/how-to-manage-workspace.md)

## <a name="setup"></a>设置

* 克隆或下载[此示例存储库](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)的内容。
* 如果下载的是 zip 文件，则提取内容。 确保文件是可读写的。
* 在设置 Azure 帐户和服务时，将名称和密钥复制到易于访问的文本文件中。 名称和密钥将添加到笔记本中的第一个单元格，其中定义了用于访问 Azure 服务的变量。
* 如果不熟悉 Azure 机器学习及其要求，最好先查看以下文档再开始使用：
 * [配置 Azure 机器学习的开发环境](../machine-learning/how-to-configure-environment.md)
 * [在 Azure 门户中创建和管理 Azure 机器学习工作区](../machine-learning/how-to-manage-workspace.md)
 * 为 Azure 机器学习配置开发环境时，请考虑使用[基于云的计算实例](../machine-learning/how-to-configure-environment.md#compute-instance)，以便更快、更轻松地开始使用。
* 将数据集文件上传到存储帐户中的容器。 如果想在笔记本中执行训练步骤，则需要较大的文件。 如果想跳过训练步骤，建议使用较小的文件。

## <a name="open-notebook-and-connect-to-azure-services"></a>打开笔记本并连接到 Azure 服务

1. 对于允许访问 Azure 服务的变量，将其所有必要信息放置在第一个单元中，然后运行该单元。
1. 运行第二个单元将确认你已为订阅连接到搜索服务。
1. 第 1.1 - 1.5 节将创建搜索服务数据存储、技能组、索引和索引器。

此时，可以选择跳过在 Azure 机器学习中创建训练数据集和试验的步骤，直接跳到对 GitHub 存储库的 models 文件夹中提供的两个模型进行注册的步骤。 如果跳过这些步骤，则在笔记本中将跳至第 3.5 节，即“编写评分脚本”。 这样可以节省时间；数据下载和上传步骤最多可能需要 30 分钟才能完成。

## <a name="creating-and-training-the-models"></a>创建和训练模型

第 2 节包含六个单元，从 nlp recipes 存储库下载手套嵌入文件。 下载之后，会将该文件上传到 Azure 机器学习数据存储。 .zip 文件约有 2G，执行这些任务将需要一些时间。 上传后，便会提取训练数据，现在你可以继续学习第 3 节。

## <a name="train-the-aspect-based-sentiment-model-and-deploy-your-endpoint"></a>训练基于方面的情绪模型并部署终结点

笔记本的第 3 节将训练在第 2 节中创建的模型，注册这些模型，并将其部署为 Azure Kubernetes 群集中的终结点。 如果不熟悉 Azure Kubernetes，强烈建议先查看以下文章再尝试创建推理群集：

* [Azure Kubernetes 服务概述](../aks/intro-kubernetes.md)
* [Azure Kubernetes 服务 (AKS) 的 Kubernetes 核心概念](../aks/concepts-clusters-workloads.md)
* [Azure Kubernetes 服务 (AKS) 中的配额、虚拟机大小限制和区域可用性](../aks/quotas-skus-regions.md)

创建和部署推理群集最多需要 30 分钟。 建议先测试 Web 服务，然后再执行最终步骤：更新技能组并运行索引器。

## <a name="update-the-skillset"></a>更新技能组

笔记本的第 4 节有四个单元，用于更新技能组和索引器。 或者，可以使用门户选择新技能并将其应用于技能组，然后运行索引器以更新搜索服务。

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Active-Learning-with-Azure-Cognitive-Search/player#time=19m35s:paused/03/player]

在门户中，转到“技能组”，然后选择“技能组定义 (JSON)”链接。 门户将显示在笔记本的第一个单元中创建的技能组的 JSON。 屏幕右侧有一个下拉菜单，可以在其中选择技能定义模板。 选择 Azure 机器学习 (AML) 模板。 提供 Azure ML 工作区的名称以及部署到推理群集的模型的终结点。 将使用终结点 URI 和密钥更新模板。

> :::image type="content" source="media/cognitive-search-aml-skill/portal-aml-skillset-definition.png" alt-text="技能组定义模板":::

从窗口复制技能组模板，然后将其粘贴到左侧的技能组定义中。 编辑模板，为以下对象提供缺失的值：

* 名称
* 说明
* 上下文
* “输入”名称和源
* “输出”名称和 targetName

保存技能组。

保存技能组后，转到索引器并选择“索引器定义 (JSON)”链接。 门户将显示在笔记本的第一个单元中创建的索引器的 JSON。 需要使用其他字段映射来更新输出字段映射，以确保索引器可以正确处理和传递它们。 保存更改，然后选择“运行”。 

## <a name="clean-up-resources"></a>清理资源

在自己的订阅中操作时，最好在项目结束时确定是否仍需要已创建的资源。 持续运行资源可能会产生费用。 可以逐个删除资源，也可以删除资源组以删除整个资源集。

可以使用左侧导航窗格中的“所有资源”或“资源组”链接 ，在门户中查找和管理资源。

如果使用的是免费服务，请记住只能设置三个索引、索引器和数据源。 可以在门户中删除单个项目，以不超出此限制。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [查看自定义技能 Web API](./cognitive-search-custom-skill-web-api.md)
> [详细了解如何将自定义技能添加到扩充管道](./cognitive-search-custom-skill-interface.md)