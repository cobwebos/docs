---
title: 部署 Web 服务
titleSuffix: ML Studio (classic) - Azure
description: 如何将训练实验转换为预测实验，为部署做好准备，然后将其部署为 Azure 机器学习 Studio （经典） web 服务。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: 4324d33a9da66c5f49645e1c8014bd5f57d37bd9
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837734"
---
# <a name="deploy-an-azure-machine-learning-studio-classic-web-service"></a>部署 Azure 机器学习 Studio （经典） web 服务

通过 Azure 机器学习 Studio （经典），可以生成并测试预测分析解决方案。 然后，可以将该解决方案部署为 Web 服务。

机器学习 Studio （经典） web 服务在应用程序和机器学习 Studio （经典）工作流评分模型之间提供了一个接口。 外部应用程序可实时与机器学习 Studio （经典）工作流评分模型通信。 对机器学习 Studio （经典） web 服务的调用会将预测结果返回到外部应用程序。 若要调用 Web 服务，需要在传递部署 Web 服务时创建的 API 密钥。 机器学习 Studio （经典） web 服务基于 REST，这是 web 编程项目的常用体系结构选择。

Azure 机器学习 Studio 的经典版具有两种类型的 web 服务：

* 请求-响应服务（RR）：对单个数据记录进行评分的低延迟、高度可缩放的服务。
* 批处理执行服务（BE）：对一批数据记录进行评分的异步服务。

BES 的输入类似于 RRS 使用的数据输入。 主要区别在于，BES 读取的记录块来自多种源，例如 Azure Blob 存储、Azure 表存储、Azure SQL 数据库、HDInsight（Hive 查询）和 HTTP 源。

概括而言，可以通过以下三个步骤来部署模型：

* **[创建训练实验]** -在经典版 Studio 中，可以使用所提供的定型数据来定型和测试预测分析模型，并使用大量的内置机器学习算法。
* **[将其转换为预测试验]** - 利用现有数据定型模型后，你就可以使用它来对新数据进行评分，为预测准备并简化你的试验。
* 将其**部署**为 **[新 Web 服务]** 或 **[经典 Web 服务]** - 将预测实验部署为 Azure Web 服务时，用户可以将数据发送到模型并接收模型的预测。

## <a name="create-a-training-experiment"></a>创建训练实验

若要训练预测分析模型，请使用经典版本的 Azure 机器学习 Studio 创建一个训练试验，其中包含用于加载定型数据的各种模块，根据需要准备数据，应用机器学习算法，以及计算结果。 可以迭代实验，尝试不同的机器学习算法来比较和评估结果。

在别处对创建和管理训练实验的过程进行了更详细地介绍。 有关详细信息，请参阅以下文章：

* [在 Azure 机器学习 Studio （经典）中创建简单实验](create-experiment.md)
* [使用 Azure 机器学习 Studio 开发预测性解决方案（经典）](tutorial-part1-credit-risk.md)
* [将定型数据导入 Azure 机器学习 Studio （经典）](import-data.md)
* [在 Azure 机器学习 Studio 中管理试验迭代（经典）](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>将训练实验转换为预测实验

模型训练完成后，便可以将训练实验转化为预测实验来对新数据进行评分。

通过转换为预测实验，可将训练的模型部署为评分 Web 服务。 Web 服务的用户会将输入的数据发送到模型，模型返回预测结果。 转换为预测实验后，需记住自己期望别人如何使用模型。

要将训练实验转换为预测实验，请单击实验画布底部的“运行”，并单击“设置 Web 服务”，选择“预测 Web 服务”。

![转换为评分实验](./media/publish-a-machine-learning-web-service/figure-1.png)

有关如何执行此转换的详细信息，请参阅[如何在 Azure 机器学习 Studio 中准备要部署的模型（经典）](convert-training-experiment-to-scoring-experiment.md)。

以下步骤描述如何将预测实验部署为新的 Web 服务。 还可将实验部署为经典 Web 服务。

## <a name="deploy-it-as-a-new-web-service"></a>部署为新 Web 服务

现在，预测实验已准备就绪，可以将其部署为新的（基于资源管理器的）Azure Web 服务。 使用 web 服务，用户可以将数据发送到模型，该模型将返回其预测。

若要部署预测实验，请单击实验画布底部的“运行”。 实验运行完毕后，单击“部署 Web 服务”或 **部署 Web 服务[全新]** 。  此时将打开机器学习 Studio （经典） Web 服务门户的 "部署" 页。

> [!NOTE] 
> 若要部署新的 Web 服务，必须对要部署 Web 服务的订阅拥有充分的权限。 有关详细信息，请参阅[使用 Azure 机器学习 Web 服务门户管理 Web 服务](manage-new-webservice.md)。 

### <a name="web-service-portal-deploy-experiment-page"></a>Web 服务门户部署试验页

在“部署实验”页上，输入 Web 服务的名称。
选择定价计划。 如果有现有的定价计划，可以选择它，否则你必须为该服务创建新的定价计划。

1. 在“定价计划”下拉菜单中选择一个现有计划，或选择“选择新计划”选项。
2. 在“计划名称”中，键入用于标识帐单上的计划的名称。
3. 从“每月计划层”中选择一个计划层。 计划层默认为默认区域的计划，并且 Web 服务将部署到该区域。

单击“部署”和“快速入门”页面，打开 Web 服务。

Web 服务快速入门页面提供了有关在创建 Web 服务后将执行的最常见任务的访问和指导。 从此处，可以轻松访问测试页和使用页。

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-new-web-service"></a>测试新 Web 服务

若要测试新 Web 服务，请在常见任务下单击“测试 Web 服务”。 在测试页上，可将 Web 服务作为请求 - 响应服务 (RRS) 或 Batch 执行服务 (BES) 进行测试。

RRS 测试页显示你为试验定义的输入、输出和任何全局参数。 若要测试 Web 服务，可手动输入适当的输入值，或提供包含测试值的逗号分隔值 (CSV) 格式的文件。

要使用 RRS 进行测试，请从列表视图模式中为输入键入适当的值，并单击“测试请求 - 响应”。 预测结果显示在左侧的输出列中。

![输入适当的值以测试 web 服务](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

若要测试 BES，请单击“Batch”。 在 Batch 测试页上，单击输入下的“浏览”，并选择包含相应示例值的 CSV 文件。 如果你没有 CSV 文件，并且使用经典版本的机器学习 Studio 创建了预测实验，则可以下载预测试验的数据集并使用它。

若要下载数据集，请打开经典版本的机器学习 Studio。 打开预测实验，右键单击实验的输入。 从上下文菜单中，选择“数据集”，并选择“下载”。

![从 Studio （经典）画布下载数据集](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

单击“测试”。 Batch 执行作业的状态显示在“测试 Batch 作业”的下方。

![通过 web 服务门户测试批处理执行作业](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

在“配置”页上，可更改描述、标题，更新存储帐户密钥，以及启用 Web 服务的示例数据。

![配置 web 服务](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>访问新 Web 服务

从机器学习 Studio 的经典版本部署 web 服务后，可以将数据发送到服务，并以编程方式接收响应。

**使用**页提供访问 Web 服务所需的所有信息。 例如，提供 API 密钥以允许对服务的授权访问。

有关访问机器学习 Studio （经典） web 服务的详细信息，请参阅[如何使用 Azure 机器学习 Studio （经典） web 服务](consume-web-services.md)。

### <a name="manage-your-new-web-service"></a>管理新 Web 服务

可以机器学习 Studio （经典） Web 服务门户管理新的 web services。 在[主门户页](https://services.azureml-test.net/)中，单击“Web 服务”。 在 Web 服务页中，可删除或复制服务。 要监视特定服务，请单击该服务，并单击“仪表板”。 若要监视与 Web 服务相关联的 Batch 作业，请单击“Batch 请求日志”。

### <a id="multi-region"></a> 将新的 Web 服务部署到多个区域

无需多个订阅或工作区即可轻松地将新的 Web 服务部署到多个区域。

定价因区域而异，因此你需要为要在其中部署 Web 服务的每个区域定义收费计划。

#### <a name="create-a-plan-in-another-region"></a>在另一个区域中创建计划

1. 登录到 [Microsoft Azure 机器学习 Web 服务](https://services.azureml.net/)。
2. 单击“计划”菜单选项。
3. 在视图页中的“计划”上，单击“新建”。
4. 从“订阅”下拉列表中，选择新计划将驻留的订阅。
5. 从“区域”下拉列表中，选择适用于新计划的区域。 所选区域的“计划选项”会显示在该页的“计划选项”部分中。
6. 从“资源组”下拉列表中，选择适用于该计划的资源组。 有关资源组的详细信息，请参阅 [Azure 资源管理器概述](../../azure-resource-manager/resource-group-overview.md)。
7. 在“计划名称”中，键入计划的名称。
8. 在“计划选项”中，单击新计划的计费级别。
9. 单击“创建”。

#### <a name="deploy-the-web-service-to-another-region"></a>将 Web 服务部署到另一个区域

1. 在 Microsoft Azure 机器学习 Web 服务页上，单击“Web 服务”菜单选项。
2. 选择要部署到新区域的 Web 服务。
3. 单击“复制”。
4. 在“Web 服务名称”中，键入 Web 服务的新名称。
5. 在“Web 服务描述”中，键入 Web 服务的描述。
6. 从“订阅”下拉列表中，选择新的 Web 服务将驻留的订阅。
7. 从“资源组”下拉列表中，选择适用于该 Web 服务的资源组。 有关资源组的详细信息，请参阅 [Azure 资源管理器概述](../../azure-resource-manager/resource-group-overview.md)。
8. 从“区域”下拉列表中，选择要部署 Web 服务的区域。
9. 从“存储帐户”下拉列表中，选择要存储 Web 服务的存储帐户。
10. 从“价格计划”下拉列表中，选择步骤 8 中所选的区域中的计划。
11. 单击“复制”。

## <a name="deploy-it-as-a-classic-web-service"></a>部署为经典 Web 服务

现在，已充分准备好预测试验，可将其部署为经典 Azure Web 服务。 使用 web 服务，用户可以将数据发送到模型，该模型将返回其预测。

要部署预测实验，请单击实验画布底部的“运行”，并单击“部署 Web 服务”。 已设置 Web 服务，现在正位于 Web 服务仪表板中。

![从工作室部署 web 服务（经典）](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>测试经典 Web 服务

可以在机器学习 Studio （经典） Web 服务门户或机器学习 Studio （经典）中测试 web 服务。

若要测试请求响应 Web 服务，请单击 Web 服务仪表板中的“测试”按钮。 将弹出一个对话框，要求输入服务的输入数据。 下面是评分实验预期的列。 输入一组数据，并单击“确定”。 Web 服务生成的结果显示在仪表板的底部。

你可以单击 "**测试**预览" 链接，在经典版本的 Azure 机器学习 Studio Web 服务门户中测试你的服务，如前面的 "新建 Web 服务" 部分所示。

若要测试 Batch 执行服务，请单击“测试”预览链接。 在 Batch 测试页上，单击输入下的“浏览”，并选择包含相应示例值的 CSV 文件。 如果你没有 CSV 文件，并且使用经典版本的机器学习 Studio 创建了预测实验，则可以下载预测试验的数据集并使用它。

![测试 Web 服务](./media/publish-a-machine-learning-web-service/figure-3.png)

在“配置”页上，可以更改服务的显示名称并提供说明。 名称和说明会显示在 [Azure 门户](https://portal.azure.com/)中，可以在其中管理 Web 服务。

可通过在**输入架构**、**输出架构**和 **Web 服务参数**下为每列输入字符串来为输入数据、输出数据和 Web 服务参数提供描述。 这些描述用于为 Web 服务提供的示例代码文档中。

可启用日志记录来诊断在访问 Web 服务时遇到的任何故障。 有关详细信息，请参阅为[机器学习 Studio （经典） web 服务启用日志记录](web-services-logging.md)。

![在 web 服务门户中启用日志记录](./media/publish-a-machine-learning-web-service/figure-4.png)

还可在 Azure 机器学习 Web 服务门户中配置 Web 服务的终结点，类似于之前在“新建 Web 服务”部分中显示的过程。 选项有所不同，可以添加或更改服务描述、启用日志记录，并启用样本数据进行测试。

### <a name="access-your-classic-web-service"></a>访问经典 Web 服务

从机器学习 Studio 的经典版本部署 web 服务后，可以将数据发送到服务，并以编程方式接收响应。

仪表板提供访问 Web 服务所需的所有信息。 例如，提供 API 密钥以允许对服务的授权访问，并提供 API 帮助页以帮助你开始编写代码。

有关访问机器学习 Studio （经典） web 服务的详细信息，请参阅[如何使用 Azure 机器学习 Studio （经典） web 服务](consume-web-services.md)。

### <a name="manage-your-classic-web-service"></a>管理经典 Web 服务

可通过执行各种操作来监视 Web 服务。 可以将其更新，也可以将其删除。 除了部署经典 Web 服务时创建的默认终结点外，还可向其添加其他终结点。

有关详细信息，请参阅[管理 Azure 机器学习 Studio （经典）工作区](manage-workspace.md)和[使用 Azure 机器学习 Studio （经典） Web 服务门户管理 web 服务](manage-new-webservice.md)。

## <a name="update-the-web-service"></a>更新 Web 服务
可更改 Web 服务，例如使用其他训练数据更新模型、重新部署、覆盖原始 Web 服务等。

要更新 Web 服务，请打开用于部署 Web 服务的原始预测实验，并单击“另存为”以创建可编辑的副本。 进行更改，并单击“部署 Web 服务”。

由于之前已部署此实验，系统会询问是要覆盖（经典 Web 服务）还是更新（新 Web 服务）现有服务。 单击“是”或“更新”将停止现有 Web 服务并部署新预测实验。

> [!NOTE]
> 如果在原始 Web 服务中进行了配置更改，例如输入新的显示名称或描述，则需要重新输入这些值。

更新 Web 服务的一种方法是以编程方式重新训练模型。 有关详细信息，请参阅[以编程方式重新训练机器学习 Studio （经典）模型](/azure/machine-learning/studio/retrain-machine-learning-model)。

## <a name="next-steps"></a>后续步骤

* 有关部署工作方式的更多技术详细信息，请参阅[机器学习 Studio （经典）模型如何从实验前进到操作化 Web 服务](model-progression-experiment-to-web-service.md)。

* 有关如何使模型准备好进行部署的详细信息，请参阅[如何在 Azure 机器学习 Studio （经典）中准备要部署的模型](convert-training-experiment-to-scoring-experiment.md)。

* 可通过多种方法来使用 REST API 和访问 Web 服务。 请参阅[如何使用 Azure 机器学习 Studio （经典） web 服务](consume-web-services.md)。

<!-- internal links -->
[创建训练实验]: #create-a-training-experiment
[将其转换为预测试验]: #convert-the-training-experiment-to-a-predictive-experiment
[新 Web 服务]: #deploy-it-as-a-new-web-service
[经典 Web 服务]: #deploy-it-as-a-classic-web-service
[全新]: #deploy-it-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service
