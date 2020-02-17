---
title: 使用自动化机器学习试验预测单车共享需求
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习工作室中的自动化机器学习训练和部署需求预测模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sacartac
ms.reviewer: nibaccam
author: cartacioS
ms.date: 01/27/2020
ms.openlocfilehash: 11e0a8a0076fb2e68c379b279f471ff74846df2e
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2020
ms.locfileid: "77088321"
---
# <a name="tutorial-forecast-bike-sharing-demand-with-automated-machine-learning"></a>教程：使用自动化机器学习预测单车共享需求
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

本教程将使用 Azure 机器学习工作室中的自动化机器学习（自动化 ML）创建一个时序预测模型，用于预测单车共享服务的租赁需求。

本教程介绍如何执行以下任务：

> [!div class="checklist"]
> * 创建并加载数据集。
> * 配置并运行自动化 ML 试验。
> * 浏览试验结果。
> * 部署最佳模型。

## <a name="prerequisites"></a>必备条件

* 企业版 Azure 机器学习工作区。 如果没有工作区，请[创建企业版工作区](how-to-manage-workspace.md)。 
    * Azure 机器学习工作室中的自动化机器学习仅适用于企业版工作区。 
* 下载 [bike-no.csv](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv) 数据文件

## <a name="get-started-in-azure-machine-learning-studio"></a>在 Azure 机器学习工作室中开始操作

本教程将在 Azure 机器学习工作室中创建自动化 ML 试验运行。机器学习工作室是一个整合的界面，其中包含的机器学习工具可让各种技能水平的数据科学实践者执行数据科学方案。 Internet Explorer 浏览器不支持此工作室。

1. 登录到 [Azure 机器学习工作室](https://ml.azure.com)。

1. 选择创建的订阅和工作区。

1. 选择“开始”。 

1. 在左窗格的“创作”部分，选择“自动化 ML”   。

1. 选择“+新建自动化 ML 运行”。  

## <a name="create-and-load-dataset"></a>创建并加载数据集

在配置试验之前，请以 Azure 机器学习数据集的形式将数据文件上传到工作区。 这可以确保数据格式适合在试验中使用。

1. 在“选择数据集”窗体中，从“+ 创建数据集”下拉列表中选择“从本地文件”。    

    1. 在“基本信息”窗体中，为数据集指定名称，并提供可选的说明。  数据集类型默认为“表格”，因为 Azure 机器学习工作室中的自动化 ML 目前仅支持表格数据集。 
    
    1. 在左下角选择“下一步” 

    1. 在“数据存储和文件选择”窗体中，选择在创建工作区期间自动设置的默认数据存储“workspaceblobstore (Azure Blob 存储)”。   这是要将数据文件上传到的存储位置。 

    1. 选择“浏览”  。 
    
    1. 在本地计算机上选择“bike-no.csv”文件。  这是作为[必备组件](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv)下载的文件。

    1. 选择“下一步” 

       上传完成后，系统会根据文件类型预先填充“设置和预览”窗体。 
       
    1. 验证“设置和预览”窗体是否已填充如下，然后选择“下一步”。  
        
        字段|说明| 教程的值
        ---|---|---
        文件格式|定义文件中存储的数据的布局和类型。| 带分隔符
        分隔符|一个或多个字符，用于指定纯文本或其他数据流中不同的独立区域之间的边界。&nbsp; |逗号
        编码|指定字符架构表中用于读取数据集的位。| UTF-8
        列标题| 指示如何处理数据集的标头（如果有）。| 使用第一个文件中的标头
        跳过行 | 指示要跳过数据集中的多少行（如果有）。| 无

    1. 通过“架构”窗体，可以进一步为此试验配置数据。  
    
        1. 对于本示例，请选择忽略 **casual** 和 **registered** 列。 这些列是 **cnt** 列的细目，因此我们不会包含这些列。

        1. 此外，对于本示例，请保留“属性”和“类型”的默认值。   
        
        1. 选择“**下一页**”。

    1. 在“确认详细信息”  窗体上，确认信息与先前在“基本信息”  和“设置和预览”  窗体上填充的内容匹配。

    1. 选择“创建”  以完成数据集的创建。

    1. 当数据集出现在列表中时，则选择它。

    1. 选择“下一步”。 

## <a name="configure-experiment-run"></a>配置试验运行

加载并配置数据后，请设置远程计算目标，并在数据中选择要预测的列。

1. 按如下所述填充“配置运行”窗体： 
    1. 输入试验名称：`automl-bikeshare`

    1. 选择“cnt”作为要预测的目标列。  此列指示共享单车的租赁总次数。

    1. 选择“创建新计算”并配置计算目标。  自动 ML 仅支持 Azure 机器学习计算。 

        字段 | 说明 | 教程的值
        ----|---|---
        计算名称 |用于标识计算上下文的唯一名称。|bike-compute
        虚拟机大小&nbsp;&nbsp;| 指定计算资源的虚拟机大小。|Standard_DS12_V2
        最小/最大节点数（在“高级设置”中）| 若要分析数据，必须指定一个或多个节点。|最小节点数：1<br>最大节点数：6
  
        1. 选择“创建”，获取计算目标。  

            **完成此操作需要数分钟的时间。** 

        1. 创建后，从下拉列表中选择新的计算目标。

    1. 选择“**下一页**”。

## <a name="select-task-type-and-settings"></a>选择任务类型和设置

通过指定机器学习任务类型和配置设置来完成自动化 ML 试验的设置。

1. 在“任务类型和设置”窗体中，选择“时序预测”作为机器学习任务类型。  

1. 选择“date”作为**时间列**，将“分组依据列”保留空白。   

    1. 选择“查看其他配置设置”  并按如下所示填充字段。 使用这些设置可以更好地控制训练作业。 否则，将会根据试验选择和数据应用默认设置。

  
        其他配置&nbsp;|说明|教程的值&nbsp;&nbsp;
        ------|---------|---
        主要指标| 对机器学习算法进行度量时依据的评估指标。|规范化均方根误差
        自动特征化| 启用预处理。 这包括自动化数据清理、准备和转换，以生成合成特征。| 启用
        解释最佳模型（预览版）| 自动显示有关自动化 ML 创建的最佳模型的可解释性。| 启用
        阻止的算法 | 要从训练作业中排除的算法| 极端随机树
        其他预测设置| 这些设置有助于改善模型的准确度 <br><br> _**预测边际**_ ：要预测的未来时间长短 <br> _**预测目标延隔**_ ：要将构建的目标变量延隔后推多久 <br> _**目标滚动窗口**_ ：指定滚动窗口的大小（例如 *max, min* 和 *sum*），将基于此大小生成特征。 |预测边际：14 <br> 预测目标延隔：&nbsp;&nbsp;无 <br> 目标滚动窗口大小：&nbsp;&nbsp;&nbsp;无
        退出条件| 如果符合某个条件，则会停止训练作业。 |训练作业时间（小时）：&nbsp;&nbsp;3 <br> 指标分数阈值：&nbsp;&nbsp;无
        验证 | 选择交叉验证类型和测试数。|验证类型：<br>k-折交叉验证&nbsp;&nbsp; <br> <br> 验证次数：5
        并发| 每次迭代执行的并行迭代的最大数目| 最大并发迭代次数：&nbsp;&nbsp;6
        
        选择“保存”。 

## <a name="run-experiment"></a>运行试验

若要运行试验，请选择“完成”。  此时会打开“运行详细信息”屏幕，其顶部的运行编号旁边显示了“运行状态”。   此状态随着试验的进行而更新。

>[!IMPORTANT]
> 准备试验运行时，准备需要 **10-15 分钟**。
> 运行以后，**每个迭代还需要 2-3 分钟**。  <br> <br>
> 在生产环境中，此过程需要一段时间，因此不妨干点其他的事。 在等待过程中，我们建议在“模型”选项卡上开始浏览已完成测试的算法。  

##  <a name="explore-models"></a>浏览模型

导航到“模型”  选项卡，以查看测试的算法（模型）。 默认情况下，这些模型在完成后按指标分数排序。 对于本教程，列表中首先显示评分最高的模型（评分根据所选的“规范化均方根误差”指标给出）。 

在等待所有试验模型完成的时候，可以选择已完成模型的“算法名称”  ，以便浏览其性能详细信息。 

以下示例将浏览“模型详细信息”和“可视化”选项卡，以查看选定模型的属性、指标和性能图表。   

![运行详细信息](./media/tutorial-automated-ml-forecast/explore-models-ui.gif)

## <a name="deploy-the-model"></a>部署模型

Azure 机器学习工作室中的自动化机器学习可以通过几个步骤将最佳模型部署为 Web 服务。 部署是模型的集成，因此它可以对新数据进行预测并识别潜在的机会领域。 

在此试验中部署到 Web 服务后，单车共享公司即会获得一个迭代且可缩放的 Web 解决方案，可以预测共享单车的租赁需求。 

运行完成后，导航回到“运行详细信息”页，然后选择“模型”选项卡。  

在此试验上下文中，根据“规范化均方根误差”指标，**StackEnsemble** 被视为最佳模型。   我们将部署此模型，但请注意，部署需要大约 20 分钟才能完成。 部署过程需要几个步骤，包括注册模型、生成资源和为 Web 服务配置资源。

1. 选择左下角的“部署最佳模型”按钮。 

1. 按如下所示填充“部署模型”窗格： 

    字段| 值
    ----|----
    部署名称| bikeshare-deploy
    部署说明| 单车共享需求部署
    计算类型 | 选择“Azure 计算实例(ACI)”
    启用身份验证| 禁用。 
    使用自定义部署资产| 禁用。 禁用此选项可以自动生成默认驱动程序文件（评分脚本）和环境文件。 
    
    本示例使用“高级”菜单中提供的默认值。  

1. 选择“部署”。   

    “运行”屏幕的顶部会以绿色字体显示一条成功消息，指出部署已成功启动。  在“建议的模型”窗格中的“部署状态”下，  
    可以看到部署进度。  
    
部署成功后，即会获得一个正常运行的、可以生成预测结果的 Web 服务。 

转到[**后续步骤**](#next-steps)详细了解如何使用新的 Web 服务，以及如何使用 Power BI 的内置 Azure 机器学习支持来测试预测。

## <a name="clean-up-resources"></a>清理资源

部署文件比数据文件和试验文件更大，因此它们的存储成本也更大。 仅当你想要最大程度地降低帐户成本，或者想要保留工作区和试验文件时，才删除部署文件。 否则，如果你不打算使用任何文件，请删除整个资源组。  

### <a name="delete-the-deployment-instance"></a>删除部署实例

若要保留资源组和工作区以便在其他教程和探索中使用，请仅从 Azure 机器学习工作室中删除部署实例。 

1. 转到 [Azure 机器学习工作室](https://ml.azure.com/)。 导航到你的工作区，然后在“资产”  窗格的左下角选择“终结点”  。 

1. 选择要删除的部署，然后选择“删除”。  

1. 选择“继续”。 

### <a name="delete-the-resource-group"></a>删除资源组

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，你已使用 Azure 机器学习工作室中的自动化 ML 创建并部署了一个可预测单车共享租赁需求的时序预测模型。 

请参阅以下文章中的步骤来创建 Power BI 支持的架构，以方便使用新部署的 Web 服务：

> [!div class="nextstepaction"]
> [使用 Web 服务](how-to-consume-web-service.md#consume-the-service-from-power-bi)


>[!NOTE]
> 此单车共享数据集已根据本教程修改。 此数据集是作为 [Kaggle 竞赛](https://www.kaggle.com/c/bike-sharing-demand/data)的一部分提供的，最初通过 [Capital Bikeshare](https://www.capitalbikeshare.com/system-data) 提供。 也可以在 [UCI 机器学习数据库](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset)中找到它。<br><br>
> 源：Fanaee-T、Hadi、Gama 和 Joao：合并系综检测器的事件标签和背景知识；人工智能的进步 (2013)：pp. 1-15，Springer Berlin Heidelberg。
