---
title: "机器学习建议 API 中的常见操作 | Microsoft Docs"
description: "Azure ML 建议示例应用程序"
services: machine-learning
documentationcenter: 
author: LuisCabrer
manager: jhubbard
editor: cgronlun
ms.assetid: 0220bc17-3315-47d7-84a3-ef490263a343
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: luisca
ROBOTS: NOINDEX
redirect_url: machine-learning-datamarket-deprecation
redirect_document_id: TRUE
ms.translationtype: Human Translation
ms.sourcegitcommit: 29c718d0c34d1e2f9d17b285a7270541a9ff15cf
ms.openlocfilehash: 9afbae9b7c24c72cbb2ef64e693f7a317dc81a46
ms.contentlocale: zh-cn
ms.lasthandoff: 02/24/2017


---
# <a name="recommendations-api-sample-application-walkthrough"></a>建议 API 示例应用程序演练
> [!NOTE]
> 应开始使用建议 API 认知服务，而非此版本。 建议认知服务将替代此服务，并且所有新功能都将在该处开发。 它具有新功能，如支持批处理、更好用的 API 资源管理器、更简洁的 API 图面、更一致的注册/计费体验等。
> 了解有关[迁移到新认知服务](http://aka.ms/recomigrate)的详细信息
> 
> 

## <a name="purpose"></a>目的
本文档显示如何通过[示例应用程序](https://code.msdn.microsoft.com/Recommendations-144df403)使用 Azure 机器学习建议 API。

此应用程序并非旨在包含完整功能，也并非为了使用所有 API。 它演示了当首次想要尝试机器学习建议服务时要执行的一些常见操作。 

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="introduction-to-machine-learning-recommendation-service"></a>机器学习建议服务简介
当基于以下数据生成建议模型时，会启用通过机器学习建议服务的建议：

* 要建议的项目的存储库，也称为目录
* 表示每个用户或会话项目用法的数据（这可以随着时间的推移通过数据采集获取，不作为示例应用的一部分）

生成建议的模型后，可根据用户所选的项目集（或单个项目）将其用于预测用户可能感兴趣的项目。

若要启用前面的方案，请在机器学习建议服务中执行以下操作：

* 创建模型：这是逻辑容器，用于保存数据（目录和用法）和预测模型。 每个模型容器通过创建时分配的唯一 ID 来标识。 此 ID 称为模型 ID，它由大部分 API 使用。 
* 上载到目录：创建模型容器后，可将其关联到目录。

**注意**：创建模型和上载到目录通常会在模型生命周期内执行一次。

* 上载用法：这会将用法数据添加到模型容器。
* 生成建议的模型：拥有足够的数据后，可以生成建议的模型。 此操作使用顶级机器学习算法来创建建议的模型。 每个生成都与唯一的 ID 相关联。 需要保留此 ID 的记录，因为它是某些 API 功能的必需项。
* 监视生成过程：建议的模型生成是异步操作，可能需要几分钟到几个小时的时间，具体取决于数据量（目录和用法）和生成参数。 因此，需要对生成进行监视。 建议的模型仅在成功完成它关联的生成时才会创建。
* （可选）选择活动的建议模型生成：仅当已在模型容器中生成多个建议模型时，才需要执行此步骤。 如果未指示活动的建议模型，系统自动将任何获取建议的请求重定向到默认的活动生成。 

**注意**：活动的建议模型可用于生产，并针对生产工作负荷进行生成。 这不同于非活动建议模型，它保持在类似于测试的环境中（有时称为过渡）。

* 获取建议：具有建议的模型后，可针对所选的单个项目或项目列表触发建议。 

通常会在一段时间内调用获取建议。 在这时间段内，可以将用法数据重定向到机器学习建议系统，这会将此数据添加到指定的模型容器。 当拥有足够的用法数据时，可以生成新的建议模型，其中包含其他用法数据。 

## <a name="prerequisites"></a>先决条件
* Visual Studio 2013 或更高版本
* Internet 访问权限 
* 订阅建议 API (https://datamarket.azure.com/dataset/amla/recommendations)。

## <a name="azure-machine-learning-sample-app-solution"></a>Azure 机器学习示例应用解决方案
此解决方案包含源代码、示例用法、目录文件和指令，以便下载编译所需的包。

## <a name="the-apis-used"></a>使用的 API
应用程序通过可用 API 的子集来使用机器学习建议功能。 应用程序中演示了以下 API：

* 创建模型：创建用于保存数据和建议模型的逻辑容器。 模型由名称标识，不能创建多个同名模型。
* 上载目录文件：用于上载目录数据。
* 上载用法文件：用于上载用法数据。
* 触发生成：用于创建建议的模型。
* 监视生成执行：用于监视建议模型生成的状态。
* 选择建议的生成模型：用于指示特定模型容器默认要使用的建议模型。 仅当你具有多个建议模型，并希望激活非活动生成作为活动的建议模型时，才需要执行此步骤。
* 获取建议：用于根据给定的单个项目或项目集检索建议的项目。 

有关 API 的完整说明，请参阅 Microsoft Azure 应用商店文档。 

**注意**：模型随着时间的推移（非同时）可以有多个生成。 每个生成会使用相同或更新的目录和其他用法数据进行创建。

## <a name="common-pitfalls"></a>常见错误
* 需要提供用户名和 Microsoft Azure 应用商店主帐户密钥才能运行示例应用。
* 连续运行示例应用将失败。 应用程序流包括创建、上载、生成监视器以及从预定义的模型获取建议；因此，如果未在调用之间更改模型名称，它将无法连续执行。
* 建议可能不会返回任何数据。 示例应用使用非常小的目录和用法文件。 因此，目录中的某些项目将不具有建议的项目。

## <a name="disclaimer"></a>免责声明
示例应用不应在生产环境中运行。 目录中提供的数据非常小，并且它将不会提供有意义的建议模型。 提供的数据作为演示之用。 


