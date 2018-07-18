---
title: 对 Azure 机器学习经典 Web 服务重新训练进行故障排除 | Microsoft 文档
description: 标识并更正重新训练 Azure 机器学习 Web 服务的模型时遇到的常见问题。
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 75cac53c-185c-437d-863a-5d66d871921e
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.openlocfilehash: 989bf010320501050a37fbf2f0799f50a5a3e2ba
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835767"
---
# <a name="troubleshooting-the-retraining-of-an-azure-machine-learning-classic-web-service"></a>对 Azure 机器学习经典 Web 服务重新训练进行故障排除
## <a name="retraining-overview"></a>重新训练概述
将预测实验部署为评分 Web 服务时，它是静态模型。 当新数据变得可用时，或当 API 使用者拥有其自己的数据时，需要重新训练模型。 

有关重新训练经典 Web 服务过程的完整演练，请参阅[以编程方式重新训练机器学习模型](retrain-models-programmatically.md)。

## <a name="retraining-process"></a>重新训练过程
当需要重新训练 Web 服务时，必须添加一些其他部分：

* 从训练试验部署的 Web 服务。 该实验必须具有已附加到**训练模型**模块的输出的 **Web 服务输出**模块。  
  
    ![将 Web 服务输出附加到训练模型。][image1]
* 已添加到评分 Web 服务的新终结点。  可使用“以编程方式重新训练机器学习模型”主题中引用的示例代码或通过 Azure 机器学习 Web 服务门户，以编程方式添加终结点。

然后，可以使用训练 Web 服务的 API 帮助页中的示例 C# 代码重新训练模型。 评估结果并对结果感到满意后，使用添加的新终结点更新训练的模型评分 Web 服务。

所有部分准备就绪后，必须按照如下主要步骤重新训练模型：

1. 调用培训 Web 服务：调用批处理执行服务 (BES)，而不是请求响应服务 (RRS)。 可使用 API 帮助页面上的示例 C# 代码进行调用。 
2. 查找 *BaseLocation*、*RelativeLocation* 和 *SasBlobToken* 的值：这些值在从调用训练 Web 服务的输出中返回。 
   ![显示重新训练示例的输出以及 BaseLocation、RelativeLocation 和 SasBlobToken 值。][image6]
3. 使用新训练的模型从评分 Web 服务更新添加的终结点：使用重新训练机器学习模型中提供的示例代码，通过训练 Web 服务中新训练的模型以编程方式更新已添加到评分模型的新终结点。

## <a name="common-obstacles"></a>常见障碍
### <a name="check-to-see-if-you-have-the-correct-patch-url"></a>检查拥有的修补程序 URL 是否正确
正在使用的修补程序 URL 必须与已添加到评分 Web 服务的新评分终结点相关联。 可通过多种方式获取修补程序 URL：

**选项 1：以编程方式**

若要获取正确的修补程序 URL：

1. 运行 [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) 示例代码。
2. 从 AddEndpoint 的输出中，查找 *HelpLocation* 值并复制 URL。
   
   ![addEndpoint 的输出中的 HelpLocation 示例。][image2]
3. 将 URL 粘贴到浏览器中，以导航到提供 Web 服务帮助链接的页面。
4. 单击“更新资源”链接，打开修补程序帮助页面。

**选项 2：使用 Azure 机器学习 Web 服务门户**

1. 登录到 [Azure 机器学习 Web 服务](https://services.azureml.net/)门户。
2. 单击“Web 服务”或顶部的“经典 Web 服务”。
4. 单击你正在使用的计分 web 服务（如果未修改的 web 服务的默认名称，其将以结尾“[Scoring Exp.]”）。
5. 单击“+新建”。
6. 添加终结点后，单击终结点名称。
7. 在“修补程序”URL 下面，单击“API 帮助”打开修补帮助页。

> [!NOTE]
> 如果已向训练 Web 服务而非预测 Web 服务添加终结点，在单击“更新资源”链接时会收到以下错误：抱歉，此功能不受支持或在此上下文中不可用。 此 Web 服务没有可更新的资源。 我们对于所造成的不便深表歉意，正在努力改进此工作流。
> 
> 

修补程序帮助页包含你必须使用的修补程序 URL，并提供可用于调用它的示例代码。

![修补程序 URL。][image5]

### <a name="check-to-see-that-you-are-updating-the-correct-scoring-endpoint"></a>检查正在更新的评分终结点是否正确
* 不修补训练 Web 服务：必须在评分 Web 服务上执行修补操作。
* 不修补 Web 服务上的默认终结点：必须在添加的新评分 Web 服务终结点上执行修补操作。

可通过访问 Web 服务门户验证终结点在哪个 Web 服务上。 

> [!NOTE]
> 确保将终结点添加到预测 Web 服务，而不是训练 Web 服务。 如果已正确部署训练和预测 Web 服务，应该看到已列出两个单独的 Web 服务。 预测 Web 服务应以“[predictive exp.]”结尾。
> 
> 

1. 登录到 [Azure 机器学习 Web 服务](https://services.azureml.net/)门户。
2. 单击“Web 服务”或“传统 Web 服务”。
3. 选择预测 Web 服务。
4. 验证是否已向 Web 服务添加新的终结点。

### <a name="check-that-your-workspace-is-in-the-same-region-as-the-web-service"></a>检查工作区是否与 Web 服务位于同一区域
1. 登录到[机器学习工作室](https://studio.azureml.net/)。
2. 在顶部，单击工作区下拉列表。

   ![机器学习区域 UI。][image4]

3. 检查工作区所在的区域。

<!-- Image Links -->

[image1]: ./media/troubleshooting-retraining-a-model/ml-studio-tm-connnected-to-web-service-out.png
[image2]: ./media/troubleshooting-retraining-a-model/addEndpoint-output.png
[image3]: ./media/troubleshooting-retraining-a-model/azure-portal-update-resource.png
[image4]: ./media/troubleshooting-retraining-a-model/check-workspace-region.png
[image5]: ./media/troubleshooting-retraining-a-model/ml-help-page-patch-url.png
[image6]: ./media/troubleshooting-retraining-a-model/retraining-output.png
[image7]: ./media/troubleshooting-retraining-a-model/web-services-tab.png
