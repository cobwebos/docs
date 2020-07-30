---
title: ML Studio （经典）：部署 web 服务-Azure
description: 如何将训练试验转换为预测试验、准备进行部署，然后将其部署为 Azure 机器学习工作室（经典）Web 服务。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: 8195d310de708e48b27b5a2d81023d02792be1d3
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2020
ms.locfileid: "87429470"
---
# <a name="deploy-an-azure-machine-learning-studio-classic-web-service"></a>部署 Azure 机器学习工作室（经典）Web 服务

**适用于：** ![是 ](../../../includes/media/aml-applies-to-skus/yes.png) 机器学习 Studio （经典） ![ no](../../../includes/media/aml-applies-to-skus/no.png)[Azure 机器学习](../compare-azure-ml-to-studio-classic.md)  


可以使用 Azure 机器学习工作室（经典）构建和测试预测分析解决方案。 然后，可以将该解决方案部署为 Web 服务。

机器学习工作室（经典）Web 服务提供了应用程序与机器学习工作室（经典）工作流评分模型之间的接口。 外部应用程序可以实时与机器学习工作室（经典）工作流评分模型进行通信。 调用机器学习工作室（经典）Web 服务可将预测结果返回外部应用程序。 若要调用 Web 服务，需要在传递部署 Web 服务时创建的 API 密钥。 机器学习工作室（经典）Web 服务基于 REST，后者是用于 Web 编程项目的一种流行的体系结构。

Azure 机器学习工作室（经典）有两种类型的 Web 服务：

* 请求响应服务 (RRS)：一种低延迟且高度可缩放的服务，用于对单个数据记录进行评分。
* 批处理执行服务 (BES)：对一批数据记录进行评分的一种异步服务。

BES 的输入类似于 RRS 使用的数据输入。 主要区别在于，BES 读取的记录块来自多种源，例如 Azure Blob 存储、Azure 表存储、Azure SQL 数据库、HDInsight（Hive 查询）和 HTTP 源。

概括而言，可以通过以下三个步骤来部署模型：

* **[创建训练实验]** - 在工作室（经典）中，你可以通过大量内置的机器学习算法使用你提供的训练数据来对预测分析模型进行训练和测试。
* **[将其转换为预测试验]** - 利用现有数据定型模型后，你就可以使用它来对新数据进行评分，为预测准备并简化你的试验。
* 将其**部署**为 **[新 Web 服务]** 或 **[经典 Web 服务]** - 将预测实验部署为 Azure Web 服务时，用户可以将数据发送到模型并接收模型的预测。

## <a name="create-a-training-experiment"></a>创建训练实验

若要训练预测分析模型，请使用 Azure 机器学习工作室（经典）创建一个训练实验，其中包括加载训练数据、准备必要数据、应用机器学习算法和评估结果的各种模块。 可以迭代实验，尝试不同的机器学习算法来比较和评估结果。

在别处对创建和管理训练实验的过程进行了更详细地介绍。 有关详细信息，请参阅以下文章：

* [在 Azure 机器学习工作室（经典）中创建一个简单的试验](create-experiment.md)
* [使用 Azure 机器学习工作室（经典）开发预测解决方案](tutorial-part1-credit-risk.md)
* [将训练数据导入 Azure 机器学习工作室（经典）中](import-data.md)
* [在 Azure 机器学习工作室（经典）中管理试验迭代](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>将训练实验转换为预测实验

模型训练完成后，便可以将训练实验转化为预测实验来对新数据进行评分。

通过转换为预测实验，可将训练的模型部署为评分 Web 服务。 Web 服务的用户会将输入的数据发送到模型，模型返回预测结果。 转换为预测实验后，需记住自己期望别人如何使用模型。

将训练实验转换为预测实验的过程涉及三个步骤：

1. 将机器学习算法模块替换为已训练模型。
2. 仅对需要评分的模块的实验进行剪裁。 训练实验包括训练所需的大量模块，但是一旦模型经过训练，则不再需要这些模块。
3. 定义模型如何接受 Web 服务用户提供的数据，以及返回什么样的数据。

> [!TIP]
> 在训练实验中，一直关注的是如何使用自己的数据对模型继续训练和评分。 但部署以后，用户会向模型发送新的数据，而该模型则会返回预测结果。 因此，将训练实验转换为预测实验并使其可供部署以后，请关注他人会如何使用该模型。

![转换为评分实验](./media/publish-a-machine-learning-web-service/figure-1.png)

### <a name="set-up-web-service-button"></a>设置 Web 服务按钮
运行实验（单击实验画布底部的“运行”）以后，请单击“设置 Web 服务”按钮（选择“预测 Web 服务”选项）。 “设置 Web 服务”执行三个步骤，将训练实验转换为预测实验：

1. 它将已训练模型保存在模块调色板的“已训练模型”部分（位于实验画布左侧）。 然后，它会将机器学习算法和[训练模型][train-model]模块替换为保存的已训练模型。
2. 它会分析实验，删除那些显然只用于训练且不再需要的模块。
3. 它会将 _Web 服务输入_和_输出_模块插入实验中的默认位置（这些模块接受并返回用户数据）。

例如，以下试验使用示例人口普查数据，训练双类提升的决策树模型：

![训练实验](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

在此实验的模块执行四个基本上不同的函数：

![模块函数](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

将此训练实验转换为预测实验时，不再需要这些模块中的某些模块，因此会将其丢弃，或者让它们充当其他用途：

* **数据** - 在评分过程中不使用示例数据集中的数据 - web 服务的用户将提供要评分的数据。 但是，训练模型使用此数据集的元数据（如数据类型）。 因此，需要将数据集保存在预测试验中，以便它可以提供此元数据。

* **准备** - 根据提交的、用于评分的用户数据，这些模块可能需要（也可能不需要）处理传入的数据。 “设置 Web 服务”按钮没有这些功能，需要确定如何处理它们。
  
    例如，在此示例中，示例数据集可能有缺失值，因此提供了一个[清理缺失数据][clean-missing-data]模块来处理它们。 另外，示例数据集还包括训练该模型时不需要的列。 因此提供[选择数据集中的列][select-columns]模块，目的是从数据流中排除那些额外的列。 如果知道提交的、用于评分的数没有缺失值，则可以移除[清理缺失数据][clean-missing-data]模块。 但是，由于[选择数据集中的列][select-columns]模块可帮助定义已训练模型所期望的数据列，需要保留该模块。

* **训练** - 这些模块用于模型的训练。 单击“设置 Web 服务”时，这些模块会被替换为单个模块，其中包含已训练的模型。 此新模块保存在模块调色板的“训练模型”部分。

* **分数** - 在此示例中，[拆分数据][split]模块用于将数据流划分为测试数据和训练数据。 在预测实验中，我们不再进行训练，因此可删除[拆分数据][split]。 同样，第二个[评分模型][score-model]模块和[评估模型][evaluate-model]模块用于比较测试数据的结果，因此预测实验不需要这些模块。 然而，其余[评分模型][score-model]模块，就需要通过 Web 服务返回分数结果。

单击“设置 Web 服务”示例如下所示：

![转换预测实验](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

要准备将实验部署为 Web 服务，“设置 Web 服务”所做的工作可能已经足够了。 但是，可能想要执行特定于试验的一些附加工作。

#### <a name="adjust-input-and-output-modules"></a>调整输入和输出模块
在训练实验中，使用定型数据集，并对其进行处理来获取窗体中机器学习算法所需的数据。 对于希望通过 Web 服务接收的数据，如果不需要上述处理，则可将其忽略：将“Web 服务输入模块”移动到试验中的另一个模块。 现在，用户的数据将到达此位置的模型。

例如，默认情况下“设置 Web 服务”将“Web 服务输入”模块置于数据流的顶部，如上图所示。 不过，我们可以手动对“Web 服务输入”定位，使其通过数据处理模块：

![移动 web 服务输入](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

通过 web 服务提供的输入数据现在直接传递到分数模型模块，无需任何预处理。

相同地，默认情况下“设置 Web 服务”将“Web 服务输入”模块放置在数据的流顶部。 在此示例中，Web 服务将[评分模型][score-model]模块的输出返回给用户，其中包括完整的输入数据向量以及评分结果。
但是，若要返回与众不同的内容，则可在 **Web 服务输出** 模块之前添加其他模块。 

例如，若要仅返回输入数据的评分结果而不是整个向量，则可添加[选择数据集中的列][select-columns]模块，以排除除计分结果之外的所有列。 然后，将“Web 服务输出”模块移动到[选择数据集中的列][select-columns]模块的输出。 该实验如下所示：

![移动 web 服务输出](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

#### <a name="add-or-remove-additional-data-processing-modules"></a>添加或删除其他数据处理模块
如果知道在评分过程不再需要试验中的多个模块，则可以删除这些模块。 例如，由于在数据处理模块后已将“Web 服务输入”移动到点，可以删除预测实验中的[清理缺失数据][clean-missing-data]模块。

现在，预测试验如下所示：

![删除其他模块](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


#### <a name="add-optional-web-service-parameters"></a>添加其他 Web 服务参数
在某些情况下，可能希望 web 服务用户在访问该服务时可以更改模块的行为。 *Web 服务参数*允许执行此操作。

常见示例为设置[导入数据][import-data]模块，以便访问 Web 服务时，已部署的 Web 服务的用户能够指定其他数据源。 或配置[导出数据][export-data]模块以指定其他目标。

可以定义 Web 服务参数并将其与一个或多个模块参数关联，可以指定它们是否是必需或可选。 访问该服务时，Web 服务的用户可为这些参数提供值，并可对模块操作进行相应的修改。

有关 Web 服务参数是什么以及其使用方法的详细信息，请参阅[使用 Azure 机器学习 Web 服务参数][webserviceparameters]。

以下步骤描述如何将预测实验部署为新的 Web 服务。 还可将实验部署为经典 Web 服务。

## <a name="deploy-it-as-a-new-web-service"></a>部署为新 Web 服务

现在，预测实验已准备就绪，可以将其部署为新的（基于资源管理器的）Azure Web 服务。 使用 web 服务，用户可以将数据发送到模型，该模型将返回其预测。

若要部署预测实验，请单击实验画布底部的“运行”。 实验运行完毕后，单击“部署 Web 服务”或 **部署 Web 服务[全新]** 。  机器学习工作室（经典）Web 服务门户的部署页随即打开。

> [!NOTE] 
> 若要部署新的 Web 服务，必须对要部署 Web 服务的订阅拥有充分的权限。 有关详细信息，请参阅[使用 Azure 机器学习 Web 服务门户管理 Web 服务](manage-new-webservice.md)。 

### <a name="web-service-portal-deploy-experiment-page"></a>Web 服务门户“部署试验”页

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

![输入合适的值来测试 Web 服务](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

若要测试 BES，请单击“Batch”。 在 Batch 测试页上，单击输入下的“浏览”，并选择包含相应示例值的 CSV 文件。 如果没有 CSV 文件，并且已使用机器学习工作室（经典）创建了预测试验，则可以下载预测试验的数据集并使用它。

若要下载数据集，请打开机器学习工作室（经典）。 打开预测实验，右键单击实验的输入。 从上下文菜单中，选择“数据集”，并选择“下载”。

![从工作室（经典）画布下载数据集](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

单击“测试”。 Batch 执行作业的状态显示在“测试 Batch 作业”的下方。

![使用 Web 服务门户测试 Batch 执行作业](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

在“配置”页上，可更改描述、标题，更新存储帐户密钥，以及启用 Web 服务的示例数据。

![配置 Web 服务](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>访问新 Web 服务

从机器学习工作室（经典）部署 Web 服务后，可以采用编程方式向服务发送数据并接收响应。

**使用**页提供访问 Web 服务所需的所有信息。 例如，提供 API 密钥以允许对服务的授权访问。

有关访问机器学习工作室（经典）Web 服务的详细信息，请参阅[如何使用 Azure 机器学习工作室（经典）Web 服务](consume-web-services.md)。

### <a name="manage-your-new-web-service"></a>管理新 Web 服务

可以通过机器学习工作室（经典）Web 服务门户管理新的 Web 服务。 在[主门户页](https://services.azureml.net/)中，单击“Web 服务”。 在 Web 服务页中，可删除或复制服务。 要监视特定服务，请单击该服务，并单击“仪表板”。 若要监视与 Web 服务相关联的 Batch 作业，请单击“Batch 请求日志”。

### <a name="deploy-your-new-web-service-to-multiple-regions"></a><a id="multi-region"></a> 将新的 Web 服务部署到多个区域

无需多个订阅或工作区即可轻松地将新的 Web 服务部署到多个区域。

定价因区域而异，因此你需要为要在其中部署 Web 服务的每个区域定义收费计划。

#### <a name="create-a-plan-in-another-region"></a>在另一个区域中创建计划

1. 登录到 [Microsoft Azure 机器学习 Web 服务](https://services.azureml.net/)。
2. 单击“计划”菜单选项。
3. 在视图页中的“计划”上，单击“新建”。
4. 从“订阅”下拉列表中，选择新计划将驻留的订阅。
5. 从“区域”下拉列表中，选择适用于新计划的区域。 所选区域的“计划选项”会显示在该页的“计划选项”部分中。
6. 从“资源组”下拉列表中，选择适用于该计划的资源组。 有关资源组的详细信息，请参阅 [Azure 资源管理器概述](../../azure-resource-manager/management/overview.md)。
7. 在“计划名称”中，键入计划的名称。
8. 在“计划选项”中，单击新计划的计费级别。
9. 单击**创建**。

#### <a name="deploy-the-web-service-to-another-region"></a>将 Web 服务部署到另一个区域

1. 在 Microsoft Azure 机器学习 Web 服务页上，单击“Web 服务”菜单选项。
2. 选择要部署到新区域的 Web 服务。
3. 单击 **“复制”** 。
4. 在“Web 服务名称”中，键入 Web 服务的新名称。
5. 在“Web 服务描述”中，键入 Web 服务的描述。
6. 从“订阅”下拉列表中，选择新的 Web 服务将驻留的订阅。
7. 从“资源组”下拉列表中，选择适用于该 Web 服务的资源组。 有关资源组的详细信息，请参阅 [Azure 资源管理器概述](../../azure-resource-manager/management/overview.md)。
8. 从“区域”下拉列表中，选择要部署 Web 服务的区域。
9. 从“存储帐户”下拉列表中，选择要存储 Web 服务的存储帐户。
10. 从“价格计划”下拉列表中，选择步骤 8 中所选的区域中的计划。
11. 单击 **“复制”** 。

## <a name="deploy-it-as-a-classic-web-service"></a>部署为经典 Web 服务

现在，已充分准备好预测试验，可将其部署为经典 Azure Web 服务。 使用 web 服务，用户可以将数据发送到模型，该模型将返回其预测。

要部署预测实验，请单击实验画布底部的“运行”，并单击“部署 Web 服务”。 已设置 Web 服务，现在正位于 Web 服务仪表板中。

![从工作室（经典）部署 Web 服务](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>测试经典 Web 服务

可在机器学习工作室（经典）Web 服务门户或机器学习工作室（经典）中测试 Web 服务。

若要测试请求响应 Web 服务，请单击 Web 服务仪表板中的“测试”按钮。 将弹出一个对话框，要求输入服务的输入数据。 下面是评分实验预期的列。 输入一组数据，并单击“确定”。 Web 服务生成的结果显示在仪表板的底部。

可以单击“测试”预览链接，在 Azure 机器学习工作室（经典）Web 服务门户中测试服务，如“新建 Web 服务”部分中所示。

若要测试 Batch 执行服务，请单击“测试”预览链接。 在 Batch 测试页上，单击输入下的“浏览”，并选择包含相应示例值的 CSV 文件。 如果没有 CSV 文件，并且已使用机器学习工作室（经典）创建了预测试验，则可以下载预测试验的数据集并使用它。

![测试 Web 服务](./media/publish-a-machine-learning-web-service/figure-3.png)

在“配置”页上，可以更改服务的显示名称并提供说明。 名称和说明会显示在 [Azure 门户](https://portal.azure.com/)中，可以在其中管理 Web 服务。

可通过在**输入架构**、**输出架构**和 **Web 服务参数**下为每列输入字符串来为输入数据、输出数据和 Web 服务参数提供描述。 这些描述用于为 Web 服务提供的示例代码文档中。

可启用日志记录来诊断在访问 Web 服务时遇到的任何故障。 有关详细信息，请参阅[为机器学习工作室（经典）Web 服务启用日志记录](web-services-logging.md)。

![在 Web 服务门户中启用日志记录](./media/publish-a-machine-learning-web-service/figure-4.png)

还可在 Azure 机器学习 Web 服务门户中配置 Web 服务的终结点，类似于之前在“新建 Web 服务”部分中显示的过程。 选项有所不同，可以添加或更改服务描述、启用日志记录，并启用样本数据进行测试。

### <a name="access-your-classic-web-service"></a>访问经典 Web 服务

从 Azure 机器学习工作室（经典）部署 Web 服务后，可以采用编程方式向服务发送数据并接收响应。

仪表板提供访问 Web 服务所需的所有信息。 例如，提供 API 密钥以允许对服务的授权访问，并提供 API 帮助页以帮助你开始编写代码。

有关访问机器学习工作室（经典）Web 服务的详细信息，请参阅[如何使用 Azure 机器学习工作室（经典）Web 服务](consume-web-services.md)。

### <a name="manage-your-classic-web-service"></a>管理经典 Web 服务

可通过执行各种操作来监视 Web 服务。 可以将其更新，也可以将其删除。 除了部署经典 Web 服务时创建的默认终结点外，还可向其添加其他终结点。

有关详细信息，请参阅[管理 Azure 机器学习工作室（经典）工作区](manage-workspace.md)和[使用 Azure 机器学习工作室（经典）Web 服务门户管理 Web 服务](manage-new-webservice.md)。

## <a name="update-the-web-service"></a>更新 Web 服务
可更改 Web 服务，例如使用其他训练数据更新模型、重新部署、覆盖原始 Web 服务等。

要更新 Web 服务，请打开用于部署 Web 服务的原始预测实验，并单击“另存为”以创建可编辑的副本。 进行更改，并单击“部署 Web 服务”。

由于之前已部署此实验，系统会询问是要覆盖（经典 Web 服务）还是更新（新 Web 服务）现有服务。 单击“是”或“更新”将停止现有 Web 服务并部署新预测实验。

> [!NOTE]
> 如果在原始 Web 服务中进行了配置更改，例如输入新的显示名称或描述，则需要重新输入这些值。

更新 Web 服务的一种方法是以编程方式重新训练模型。 有关详细信息，请参阅[以编程方式重新训练机器学习工作室（经典）模型](/azure/machine-learning/studio/retrain-machine-learning-model)。

## <a name="next-steps"></a>后续步骤

* 有关部署如何工作的详细技术细节，请参阅[机器学习工作室（经典）模型如何从实验逐步变为运营 Web 服务](model-progression-experiment-to-web-service.md)。

* 有关如何使模型就绪可部署的详细信息，请参阅[如何准备模型以便在 Azure 机器学习工作室（经典）中进行部署](convert-training-experiment-to-scoring-experiment.md)。

* 可通过多种方法来使用 REST API 和访问 Web 服务。 请参阅[如何使用 Azure 机器学习工作室（经典）Web 服务](consume-web-services.md)。

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

[webserviceparameters]: web-service-parameters.md
[deploy]: deploy-a-machine-learning-web-service.md
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
