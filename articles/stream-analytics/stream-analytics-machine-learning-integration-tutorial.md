---
title: Azure 流分析与 Azure 机器学习的集成
description: 本文介绍如何使用用户定义的函数快速设置集成了 Azure 机器学习的简单 Azure 流分析作业。
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/01/2018
ms.openlocfilehash: 93397e5370863b11b7c153bbf234d6bfdd808718
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="performing-sentiment-analysis-by-using-azure-stream-analytics-and-azure-machine-learning"></a>使用 Azure 流分析和 Azure 机器学习执行情绪分析
本文介绍了如何快速设置集成了 Azure 机器学习的简单 Azure 流分析作业。 你将使用 Cortana Intelligence 库中的机器学习情绪分析模型来实时分析流文本数据并确定情绪分数。 可以使用 Cortana Intelligence Suite 完成此任务，不必担心构建情绪分析模型的复杂性。

可以将从本文学习的知识应用于诸如下列方案：

* 在流式处理 Twitter 数据时分析实时情绪。
* 分析客户与支持人员之间的聊天记录。
* 评估论坛、博客和视频上的评论。 
* 许多其他实时的预测性评分方案。

在实际方案中，将直接从 Twitter 数据流获取数据。 为简化教程，我们已将教程编写为让流分析作业从 Azure Blob 存储中的 CSV 文件获取推文。 可以创建自己的 CSV 文件，也可以使用示例 CSV 文件，如下图中所示：

![CSV 文件中的示例推文](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

你创建的流分析作业将情绪分析模型用作用户定义的函数 (UDF)，用于处理来自 blob 存储的示例文本数据。 输出（情绪分析的结果）写入到同一 blob 存储中的另一个 CSV 文件中。 

下图显示了此配置。 如上所述，对于更现实可行的方案，可将 Blob 存储替换为 Azure 事件中心输入中的 Twitter 流数据。 此外，还可以构建 [Microsoft Power BI](https://powerbi.microsoft.com/)，以实时查看聚合情绪。    

![流分析机器学习集成概述](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>先决条件
在开始之前，请确保具有以下各项：

* 一个有效的 Azure 订阅。
* 包含某些数据的 CSV 文件。 可以从 [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv) 下载前面所示的文件，也可以创建自己的文件。 对于本文，我们假定使用的是来自 GitHub 的文件。

概括来说，若要完成本文展示的任务，需要执行以下操作：

1. 创建一个 Azure 存储帐户和一个 blob 存储容器，并将一个 CSV 格式的输入文件上传到该容器。
3. 将 Cortana Intelligence 库中的一个情绪分析模型添加到 Azure 机器学习工作区，并将该模型部署为机器学习工作区中的 Web 服务。
5. 创建将此 Web 服务作为函数进行调用的流分析作业，以确定所输入文本的情绪。
6. 启动流分析作业，并检查输出。

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>创建一个存储容器并上传 CSV 输入文件
对于此步骤，可以使用任何 CSV 文件，例如从 GitHub 获取的文件。

1. 在 Azure 门户中，单击“创建资源” > “存储” > “存储帐户”。

2. 提供一个名称（在本例中为 `samldemo`）。 该名称只能使用小写字母和数字，并且在 Azure 中必须唯一。 

3. 指定一个现有资源组，并指定位置。 对于位置，建议为本教程中创建的所有资源使用同一位置。

    ![提供存储帐户详细信息](./media/stream-analytics-machine-learning-integration-tutorial/create-sa1.png)

4. 在 Azure 门户中，选择存储帐户。 在存储帐户边栏选项卡中，单击“容器”，然后单击**+“容器”以创建 blob 存储。&nbsp;**

    ![创建 blob 容器](./media/stream-analytics-machine-learning-integration-tutorial/create-sa2.png)

5. 为容器提供名称（在本例中为 `azuresamldemoblob`），并验证“访问类型”是否已设置为“blob”。 完成后，单击“确定”。

    ![指定 blob 容器详细信息](./media/stream-analytics-machine-learning-integration-tutorial/create-sa3.png)

6. 在“容器”边栏选项卡中，选择新容器，这将打开该容器的边栏选项卡。

7. 单击“上传” 。

    ![容器的“上传”按钮](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. 在“上传 Blob”边栏选项卡中，上传之前下载的 sampleinput.csv 文件。 对于“Blob 类型”，选择“块 blob”并将块大小设置为 4 MB，这对于本教程已足够。

9. 单击边栏选项卡底部的“上传”按钮。

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>从 Cortana Intelligence 库中添加情绪分析模型

现在，示例数据已在 blob 中，可以启用 Cortana Intelligence 库中的情绪分析模型了。

1. 转到 Cortana Intelligence 库中的[预测情绪分析模型](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1)页面。  

2. 单击“在工作室中打开”。  
   
   ![流分析机器学习，打开机器学习工作室](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. 登录并转到工作区。 选择一个位置。

4. 在页面底部单击“运行”。 进程将运行，这将花费大约一分钟。

   ![在机器学习工作室中运行试验](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. 在进程成功运行后，在页面底部选择“部署 Web 服务”。

   ![在机器学习工作室中将试验部署为 Web 服务](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. 若要验证情绪分析模型是否已就绪可供使用，请单击“测试”按钮。 提供文本输入，例如“I love Microsoft”。 

   ![在机器学习工作室中测试试验](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    如果测试正常运行，将显示类似于以下示例的结果：

   ![机器学习工作室中的测试结果](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. 在“应用”列中，单击“Excel 2010 或更早版本的工作簿”链接以下载 Excel 工作簿。 该工作簿包含稍后设置流分析作业时所需的 API 密钥和 URL。

    ![流分析机器学习，速览](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>创建使用机器学习模型的流分析作业

现在可以创建一个从 blob 存储中的 CSV 文件读取示例推文的流分析作业。 

### <a name="create-the-job"></a>创建作业

1. 转到 [Azure 门户](https://portal.azure.com)。  

2. 单击“创建资源” > “物联网” > “流分析作业”。 

3. 将作业命名为 `azure-sa-ml-demo`，指定一个订阅，指定一个现有资源组或新建一个资源组，然后为作业选择位置。

   ![指定新的流分析作业的设置](./media/stream-analytics-machine-learning-integration-tutorial/create-job-1.png)
   

### <a name="configure-the-job-input"></a>配置作业输入
该作业从之前上传到 blob 存储的 CSV 文件获取其输入。

1. 在创建作业后，在作业边栏选项卡中的“作业拓扑”下，单击“输入”选项。    

2. 在“输入”边栏选项卡中，单击“添加流输入” >“Blob 存储”

3. 使用以下值填写“Blob 存储”边栏选项卡：

   
   |字段  |值  |
   |---------|---------|
   |**输入别名** | 使用名称 `datainput` 并选择“从订阅选择 Blob 存储”       |
   |**存储帐户**  |  选择前面创建的存储帐户。  |
   |**容器**  | 选择前面创建的容器 (`azuresamldemoblob`)        |
   |**事件序列化格式**  |  选择“CSV”       |

   ![新作业输入的设置](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

4. 单击“ **保存**”。

### <a name="configure-the-job-output"></a>配置作业输出
作业将结果发送到它从中获取输入的同一 blob 存储。 

1. 在作业边栏选项卡中的“作业拓扑”下，单击“输出”选项。  

2. 在“输出”边栏选项卡中，单击“添加” >“Blob 存储”，然后添加别名为 `datamloutput` 的输出。 

3. 使用以下值填写“Blob 存储”边栏选项卡：

   |字段  |值  |
   |---------|---------|
   |**输出别名** | 使用名称 `datainput` 并选择“从订阅选择 Blob 存储”       |
   |**存储帐户**  |  选择前面创建的存储帐户。  |
   |**容器**  | 选择前面创建的容器 (`azuresamldemoblob`)        |
   |**事件序列化格式**  |  选择“CSV”       |

   ![新作业输出的设置](./media/stream-analytics-machine-learning-integration-tutorial/create-output2.png) 

4. 单击“ **保存**”。   


### <a name="add-the-machine-learning-function"></a>添加机器学习函数 
在前面，你向 Web 服务发布了一个机器学习模型。 在我们的方案中，当流分析作业运行时，它将来自输入的每个示例推文发送到用于情绪分析的 Web 服务。 机器学习 Web 服务返回一种情绪（`positive`、`neutral` 或 `negative`）和推文为正面内容的概率。 

在教程的本部分中，将在流分析作业中定义一个函数。 可以调用该函数来将推文发送到 Web 服务并返回响应。 

1. 请确保具有之前在 Excel 工作簿中下载的 Web 服务 URL 和 API 密钥。

2. 导航到作业边栏选项卡 >“函数” > “+ 添加” > “AzureML”

3. 使用以下值填写“Azure 机器学习函数”边栏选项卡：

   |字段  |值  |
   |---------|---------|
   | **函数别名** | 使用名称 `sentiment` 并选择“手动提供 Azure 机器学习函数设置”，该选项会提供输入 URL 和密钥的选项。      |
   | **URL**| 粘贴 Web 服务 URL。|
   |**键** | 粘贴 API 密钥。 |
  
   ![用于向流分析作业添加机器学习函数的设置](./media/stream-analytics-machine-learning-integration-tutorial/add-function.png)  
    
4. 单击“ **保存**”。

### <a name="create-a-query-to-transform-the-data"></a>创建查询来转换数据

流分析使用声明性的、基于 SQL 的查询来检查输入，并对其进行处理。 在本部分中，将创建一个查询，该查询读取输入中的每条推文，之后调用机器学习函数来执行情绪分析。 然后，该查询将结果发送到你定义的输出（blob 存储）。

1. 返回到作业概述边栏选项卡。

2.  在“作业拓扑”下，单击“查询”框。

3. 输入以下查询：

    ```
    WITH sentiment AS (  
    SELECT text, sentiment(text) as result from datainput  
    )  

    Select text, result.[Score]  
    Into datamloutput
    From sentiment  
    ```    

    该查询调用前面创建的函数 (`sentiment`) 来对输入中的每条推文执行情绪分析。 

4. 单击“保存”，保存查询。


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>启动流分析作业并检查输出

现在可以启动流分析作业。

### <a name="start-the-job"></a>启动作业
1. 返回到作业概述边栏选项卡。

2. 单击边栏选项卡顶部的“启动”。

3. 在“启动作业”中，选择“自定义”，然后选择将 CSV 文件上传到 Blob 存储之前的某一天。 完成后，单击“启动”。  


### <a name="check-the-output"></a>检查输出
1. 让作业运行几分钟，直到在“监视”框中看到活动。 

2. 如果你有通常用来观察 blob 存储内容的工具，可使用该工具来观察 `azuresamldemoblob` 容器。 另外，也可以在 Azure 门户中执行以下步骤：

    1. 在门户中，找到 `samldemo` 存储帐户，然后在该帐户中找到 `azuresamldemoblob` 容器。 可以在该容器中看到两个文件：包含示例推文的文件和由流分析作业生成的 CSV 文件。
    2. 右键单击生成的文件并选择“下载”。 

   ![从 Blob 存储下载 CSV 作业输出](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. 打开生成的 CSV 文件。 可以看到类似以下示例的内容：  
   
   ![流分析机器学习，CSV 视图](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>查看指标
还可以查看与 Azure 机器学习函数相关的指标。 作业边栏选项卡中的“监视”框中会显示以下与函数相关的指标：

* **函数请求数**指示发送到机器学习 Web 服务的请求数。  
* **函数事件**指示请求中的事件数。 默认情况下，发送到机器学习 Web 服务的每个请求包含最多 1000 个事件。  


## <a name="next-steps"></a>后续步骤

* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [集成 REST API 和机器学习](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)



