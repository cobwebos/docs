---
title: "使用 Azure 流分析和 Azure 机器学习进行情绪分析 | Microsoft 文档"
description: "如何在流分析作业中使用用户定义的函数和机器学习"
keywords: 
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: cfced01f-ccaa-4bc6-81e2-c03d1470a7a2
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 10/04/2016
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: fd5d7e2bf8d9cf68f1c3e9fe98656a8cfe0d1f15


---
# <a name="sentiment-analysis-by-using-azure-stream-analytics-and-azure-machine-learning"></a>使用 Azure 流分析和 Azure 机器学习进行情绪分析
本文旨在帮助你快速设置简单的 Azure 流分析作业，该作业还集成了 Azure 机器学习。 我们将使用 Cortana Intelligence 库中的情绪分析机器学习模型来分析流文本数据，并且实时确定情绪分数。 除了其他许多实时、可预测的得分方案外，本文中的信息还可帮助你了解 Twitter 流数据的实时情绪分析、分析客户与支持人员的聊天记录以及评估论坛、博客和视频评论等方案。

本文提供作为 Azure Blob 存储中输入的示例 CSV 文件（包含文本），如下图所示。 该作业在 blob 存储的示例文本数据上将情绪分析模型用作用户定义的函数 (UDF)。 最终结果位于其他 CSV 文件中的相同 blob 存储中。 

![流分析机器学习](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

下图显示了此配置。 对于更现实可行的方案，可将 Blob 存储替换为 Azure 事件中心输入中的 Twitter 流数据。 此外，还可以构建 [Microsoft Power BI](https://powerbi.microsoft.com/)，以实时查看聚合情绪。    

![流分析机器学习](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>先决条件
完成本文所展示任务的先决条件如下所示：

* 一个有效的 Azure 订阅。
* 包含某些数据的 CSV 文件。 可从 [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample Data/sampleinput.csv) 中下载图 1 中所示文件，或创建自己的文件。 对于本文，我们假设使用 GitHub 上供下载的文件。

从较高层面上说，若要完成本文展示的任务，需要执行以下操作：

1. 将 CSV 输入文件上传到 Azure Blob 存储。
2. 将 Cortana Intelligence 库中的情绪分析模型添加到 Azure 机器学习工作区。
3. 在机器学习工作区中，将此模型部署为 Web 服务。
4. 创建将此 Web 服务调用为函数的流分析作业，确定所输入文本的情绪。
5. 启动流分析作业，并观察输出。

## <a name="upload-the-csv-input-file-to-blob-storage"></a>将 CSV 输入文件上传到 Blob 存储
对于此步骤，可以使用任何 CSV 文件，例如已经指定为可在 GitHub 上下载的文件。 可使用 [Azure 存储资源管理器](http://storageexplorer.com/)或 Visual Studio 上传文件，或者使用自定义代码。 我们使用基于 Visual Studio 的示例。

1. 在 Visual Studio 中，依次单击“Azure” > “存储” > “附加外部存储”。 输入“帐户名称”和“帐户密钥”。  
   
   ![流分析机器学习，服务器资源管理器](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-server-explorer.png)  
2. 展开在步骤 1 中附加的存储、单击“创建 Blob 容器”，然后输入逻辑名称。 创建容器后，打开它，查看其内容。 （此时它将为空）。  
   
   ![流分析机器学习，创建 blob](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-create-blob.png)  
3. 若要上传 CSV 文件，请单击“上传 Blob”，然后单击“本地磁盘中的文件”。  

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>从 Cortana Intelligence 库中添加情绪分析模型
1. 从 Cortana Intelligence 库中下载[预测情绪分析模型](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1)。  
2. 在 Machine Learning Studio 中，单击“在 Studio 中打开”。  
   
   ![流分析机器学习，打开 Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  
3. 登录并转到工作区。 选择最适合自己的位置。
4. 在页面底部单击“运行”。  
5. 在进程成功运行后，单击“部署 Web 服务”。
6. 情绪分析模型即可使用。 若要验证，请单击“测试”按钮，并输入文本，例如“我爱 Microsoft”。 该测试应该会返回类似以下内容的结果：

`'Predictive Mini Twitter sentiment analysis Experiment' test returned ["4","0.715057671070099"]...`  

![流分析机器学习，分析数据](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-data.png)  

在“应用”列中，单击“Excel 2010 或较早的工作簿”，获取以后设置流分析作业所需的 API 密钥和 URL。 （仅在从其他 Azure 帐户工作区使用机器学习模型时需要用到此步骤。 本文假定情况就是如此，方便解决该方案。）  

请记下已下载的 Excel 文件的 Web 服务 URL 和访问密钥，如下所示：  

![流分析机器学习，速览](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  

## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>创建使用机器学习模型的流分析作业
1. 转到 [Azure 门户](https://manage.windowsazure.com)。  
2. 依次单击“新建” > “数据服务” > “流分析” > “快速创建”。 在“作业名称”中输入作业的名称、在“区域”中输入作业的相应区域，并且在“区域监视存储帐户”中选择帐户。    
3. 创建作业后，在“输入”选项卡上，单击“添加输入”。  
   
   ![流分析机器学习，添加机器学习输入](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-input-screen.png)  
4. 在“添加输入”向导首页上单击“数据流”，然后单击“下一步”。 在下一页上，单击“Blob 存储”作为输入，然后单击“下一步”。  
5. 在向导的“Blob 存储设置”页上，提供之前在上传数据时定义的存储帐户 blob 容器名称。 单击“下一步” 。 对于“事件序列化格式”，请单击“CSV”。 接受“序列化设置”页剩余部分的默认值。 单击 **“确定”**。  
6. 在“输出”选项卡上，单击“添加输出”。  
   
   ![流分析机器学习，添加输出](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-output-screen.png)  
7. 单击“Blob 存储”，然后输入相同的参数，容器参数除外。 “输入”值已配置为从上传 **CSV** 文件、名为“test”的容器中读取。 对于“输出”，请输入“testoutput”。 容器名称必须不同。 验证此容器确实存在。     
8. 单击“下一步”，配置输出的“序列化设置”。 对于“输入”，单击“CSV”，然后单击“确定”按钮。
9. 在“函数”选项卡上，单击“添加机器学习函数”。  
   
   ![流分析机器学习，添加机器学习函数](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-ml-function.png)  
10. 在“机器学习 Web 服务设置”页上，找到机器学习工作区、Web 服务和默认终结点。 对于本文，如果知道 URL 并且具有 API 密钥，请手动应用设置，熟悉为任何工作区配置 Web 服务的操作。 输入终结点 **URL** 和 **API 密钥**。 单击 **“确定”**。    
    
    ![流分析机器学习，机器学习 Web 服务](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-web-service.png)    
11. 在“查询”选项卡上，修改查询，如下所示：    
    
    ```
    WITH subquery AS (  
      SELECT text, sentiment(text) as result from input  
    )  
    
    Select text, result.[Scored Labels]  
    Into output  
    From subquery  
    ```    
12. 单击“保存”，保存查询。

## <a name="start-the-stream-analytics-job-and-observe-the-output"></a>启动流分析作业，并观察输出
1. 在作业页面底部单击“启动”。
2. 在“启动查询”对话框上，单击“自定义时间”，然后选择一个早于将 CSV 上传到 Blob 存储的时间。 单击 **“确定”**。  
   
   ![流分析机器学习，自定义时间](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-custom-time.png)  
3. 使用用于上传 CSV 文件的工具（例如 Visual Studio）转到 Blob 存储。
4. 启动作业几分钟后，输出容器即已创建，并且 CSV 文件将上传到其中。  
5. 在默认 CSV 编辑器中打开该文件。 所示内容应类似于以下内容：  
   
   ![流分析机器学习，CSV 视图](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  

## <a name="conclusion"></a>结束语
本文演示如何创建实时读取流文本数据和将情绪分析应用到该数据的流分析作业。 完成所有这些操作无需担心构建情绪分析模型会很复杂。 这是 Cortana Intelligence 套件的优势之一。

还可以查看与 Azure 机器学习函数相关的指标。 为此，请单击“监视”选项卡。 显示三个与函数相关的指标。  

* **函数请求数**指示发送到机器学习 Web 服务的请求数。  
* **函数事件**指示请求中的事件数。 默认情况下，发送到机器学习 Web 服务的每个请求包含最多 1000 个事件。  
* **失败的函数请求**指示发送到机器学习 Web 服务的失败请求数。  
  
    ![流分析机器学习，机器学习监视视图](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-monitor-view.png)  




<!--HONumber=Nov16_HO3-->


