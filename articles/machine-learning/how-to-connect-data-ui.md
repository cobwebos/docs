---
title: 连接到 Azure 上存储服务中的数据
titleSuffix: Azure Machine Learning
description: 创建数据存储和数据集，以通过 Azure 机器学习 studio 安全连接到 Azure 中的存储服务中的数据。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 09/22/2020
ms.custom: how-to
ms.openlocfilehash: feb79a2a077f819cce22925f23f5ed640d05e8d3
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91296665"
---
# <a name="connect-to-data-with-the-azure-machine-learning-studio"></a>在 Azure 机器学习 studio 中连接到数据

本文介绍如何使用 [Azure 机器学习 studio](overview-what-is-machine-learning-studio.md)访问数据。 通过 [Azure 机器学习数据存储](how-to-access-data.md)连接到 Azure 上存储服务中的数据，然后将该数据打包到 ML 工作流中具有 [Azure 机器学习数据集](how-to-create-register-datasets.md)的任务。

下表定义和汇总了数据存储和数据集的优点。 

|Object|说明| 优点|   
|---|---|---|
|数据存储| 通过在与工作区关联的 [Key Vault](https://azure.microsoft.com/services/key-vault/) 中存储连接信息（如订阅 ID 和令牌授权），安全地连接到 Azure 上的存储服务 | 由于你的信息已安全存储，你 <br><br> <li> 不要 &nbsp; 将 &nbsp; 身份验证 &nbsp; 凭据 &nbsp; 或 &nbsp; 原始 &nbsp; 数据源置于风险之中。 <li> 不再需要在脚本中对其进行硬编码。
|数据集| 通过创建数据集，可以创建对数据源位置的引用及其元数据的副本。 利用数据集，你可以 <br><br><li> 在模型定型过程中访问数据。<li> 与其他用户共享数据和展开协作。<li> 利用开源库（如 pandas）进行数据浏览。 | 由于数据集是延迟计算的，并且数据仍保留在其现有位置，因此 <br><br><li>在存储中保留数据的单个副本。<li> 不产生额外的存储成本 <li> 不会无意中更改原始数据源。<li>会提高 ML 工作流性能速度。 

若要了解数据存储和数据集适合 Azure 机器学习总体数据访问工作流的位置，请参阅 [安全访问数据](concept-data.md#data-workflow) 一文。

有关代码首次体验，请参阅以下文章，使用 [Azure 机器学习 PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py&preserve-view=true) 执行以下操作：
* [通过数据存储连接到 Azure 存储服务](how-to-access-data.md)。 
* [创建 Azure 机器学习数据集](how-to-create-register-datasets.md)。 

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 试用 [Azure 机器学习的免费版或付费版](https://aka.ms/AMLFree)。

- [Azure 机器学习 studio](https://ml.azure.com/)的访问权限。

- Azure 机器学习工作区。 [创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

    -  创建工作区时，会将 Azure Blob 容器和 Azure 文件共享作为数据存储自动注册到工作区。 它们分别命名为 `workspaceblobstore` 和 `workspacefilestore`。 如果 blob 存储满足你的需求，则将 `workspaceblobstore` 设置为默认数据存储，并且已配置为使用。 否则，你需要在 Azure 上使用 [支持的存储类型](how-to-access-data.md#matrix)的存储帐户。
    

## <a name="create-datastores"></a>创建数据存储

可从[这些 Azure 存储解决方案](how-to-access-data.md#matrix)创建数据存储。 **对于不支持的存储解决方案**，以及在 ML 试验期间节省数据出口成本，你必须 [将数据移动](how-to-access-data.md#move) 到受支持的 Azure 存储解决方案。 [了解有关数据存储的详细信息](how-to-access-data.md)。 



在 Azure 机器学习工作室中通过几个步骤创建新的数据存储。

> [!IMPORTANT]
> 如果数据存储帐户位于虚拟网络中，则需要执行其他配置步骤以确保工作室可以访问你的数据。 请参阅 [网络隔离 & 隐私性](how-to-enable-virtual-network.md#machine-learning-studio) ，以确保应用适当的配置步骤。

1. 登录到 [Azure 机器学习工作室](https://ml.azure.com/)。
1. 在左窗格中的“管理”下，选择“数据存储” 。
1. 选择“+ 新建数据存储”。
1. 填写新数据存储的表单。 该表单会根据你选择的 Azure 存储类型和身份验证类型智能地进行更新。 请参阅[存储访问和权限部分](#access-validation)，了解在哪里可以找到填充此窗体所需的身份验证凭据。

下面的示例展示了创建 **Azure Blob 数据存储**时窗体的外观：

![新数据存储的表单](media/how-to-connect-data-ui/new-datastore-form.png)

## <a name="create-datasets"></a>创建数据集

创建数据存储后，创建数据集以与数据进行交互。 数据集可将数据打包成一个延迟计算的可供机器学习任务（例如训练）使用的对象。 [了解有关数据集的详细信息](how-to-create-register-datasets.md)。

有两种类型的数据集： FileDataset 和 TabularDataset。 
[FileDatasets](how-to-create-register-datasets.md#filedataset) 创建对单个或多个文件或公共 url 的引用。 而 [TabularDatasets](how-to-create-register-datasets.md#tabulardataset) 以表格格式表示数据。 

以下步骤和动画演示如何在 [Azure 机器学习工作室](https://ml.azure.com)中创建数据集。

> [!Note]
> 通过 Azure 机器学习工作室创建的数据集会自动注册到工作区。

![使用 UI 创建数据集](./media/how-to-connect-data-ui/create-dataset-ui.gif)

若要在工作室中创建数据集：
1. 登录到 [Azure 机器学习 studio](https://ml.azure.com/)。
1. 在左侧窗格的“资产”部分，选择“数据集”。 
1. 选择“创建数据集”以选择数据集的源。 此源可以是本地文件、数据存储、公共 Url 或 [Azure 开放数据集](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md)。
1. 为“数据集类型”选择“表格”或“文件”。 
1. 选择“下一步”，打开“数据存储和文件选择”窗体。 在此窗体上，可以选择在创建数据集后保留数据集的位置，还可以选择要用于数据集的具体数据文件。
    1. 如果数据位于虚拟网络中，请启用“跳过验证”。 详细了解[虚拟网络隔离和隐私](how-to-enable-virtual-network.md#machine-learning-studio)。
1. 选择“下一步”以填充“设置和预览”以及“架构”窗体；它们是根据文件类型智能填充的。在这些窗体上进行创建之前，可以进一步配置数据集。   
1. 选择“下一步”，查看“确认详细信息”窗体。 检查所做的选择，为数据集创建可选的数据配置文件。 详细了解[数据分析](#profile)。
1. 选择“创建”以完成数据集的创建。

<a name="profile"></a>

### <a name="data-profile-and-preview"></a>数据配置文件和预览

创建数据集后，验证是否可以在工作室中查看配置文件和预览，步骤如下。 

1. 登录到 [Azure 机器学习 studio](https://ml.azure.com/)
1. 在左侧窗格的“资产”部分，选择“数据集”。 
1. 选择要查看的数据集的名称。 
1. 选择”**浏览**“选项卡。 
1. 选择 " **预览** " 或 " **配置文件** " 选项卡。 

![查看数据集配置文件和预览](./media/how-to-connect-data-ui/dataset-preview-profile.gif)

可以获取整个数据集的各种摘要统计信息，以验证该数据集是否随时可在机器学习中使用。 对于非数字列，仅包括最小值、最大值和误差计数等基本统计信息。 对于数字列，还可以查看其统计时刻和估算的分位数。 

具体而言，Azure 机器学习数据集的数据配置文件包括：

>[!NOTE]
> 对于具有不相关类型的特征，将显示空白条目。

|统计信息|说明
|------|------
|功能| 正在汇总的列的名称。
|配置文件| 基于推理的类型显示的内联可视化效果。 例如，字符串、布尔值和日期包含值计数，而小数（数字）则包含近似的直方图。 这样，就可以快速了解数据的分布。
|类型分布| 列中类型的内联值计数。 Null 是其自身的类型，因此，此可视化效果可用于检测反常值或缺失值。
|类型|列的推理类型。 可能的值包括：字符串、布尔值、日期和小数。
|Min| 列的最小值。 对于类型不具有固有排序 (如) 的功能，将显示空白项。
|Max| 列的最大值。 
|Count| 列中缺失和未缺失条目的总数。
|非缺失计数| 列中未缺失的条目数。 空字符串和误差被视为值，因此它们不会计入“未缺少计数”。
|分位数| 每个分位数中的近似值，用于提供数据分布的概观。
|平均值| 列的算术中间值或平均值。
|标准偏差| 此列数据的离散量或差异量的度量。
|Variance| 此列数据与其平均值之间的分散程度度量。 
|倾斜| 此列数据与正态分布之间的差异程度度量。
|峰度| 此列数据与正态分布相比的落后程度度量。

## <a name="storage-access-and-permissions"></a>存储访问和权限

若要确保安全连接到 Azure 存储服务，Azure 机器学习要求你有权访问相应的数据存储。 此访问权限依赖用于注册数据存储的身份验证凭据。

### <a name="virtual-network"></a>虚拟网络

如果你的数据存储帐户在虚拟网络中，则需要执行其他配置步骤来确保 Azure 机器学习能够访问你的数据。 请参阅[网络隔离和隐私](how-to-enable-virtual-network.md#machine-learning-studio)，以确保在创建和注册数据存储时应用适当的配置步骤。  

### <a name="access-validation"></a>访问验证

在**初始数据存储创建和注册过程**中，Azure 机器学习会自动验证基础存储服务是否存在，用户提供的主体 (用户名、服务主体或 SAS 令牌) 是否有权访问指定的存储。

创建数据存储后，此验证只针对要求访问基础存储容器的方法执行，而不是每次检索数据存储对象时都执行 。 例如，如果要从数据存储中下载文件，则会进行验证，但如果只想更改默认数据存储，则不会进行验证。

若要对基础存储服务的访问进行身份验证，可以根据要创建的数据存储类型提供帐户密钥、共享访问签名 (SAS) 令牌或服务主体。 [存储类型矩阵](how-to-access-data.md#matrix)列出了与各种数据存储类型对应的受支持的身份验证类型。

可在 [Azure 门户](https://portal.azure.com)上查找帐户密钥、SAS 令牌和服务主体信息。

* 如果计划使用帐户密钥或 SAS 令牌进行身份验证，请在左窗格中选择“存储帐户”，然后选择要注册的存储帐户。
  * “概述”页面提供了帐户名称、容器和文件共享名称等信息。
      1. 对于帐户密钥，请转到“设置”窗格中的“访问密钥” 。
      1. 对于 SAS 令牌，请转到“设置”窗格中的“共享访问签名” 。

* 如果计划使用 [服务主体](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) 进行身份验证，请使用 **应用注册** ，并选择要使用的应用。
    * 其对应的“概览”页面将包含租户 ID 和客户端 ID 之类的必需信息。

> [!IMPORTANT]
> 出于安全原因，你可能需要更改 Azure 存储帐户的访问密钥（帐户密钥或 SAS 令牌）。 执行此操作时，请确保将新凭据与工作区及其连接的数据存储进行同步。 了解如何 [同步已更新的凭据](how-to-change-storage-access-key.md)。

### <a name="permissions"></a>权限

对于 Azure Blob 容器和 Azure Data Lake 第 2 代存储，请确保身份验证凭据具有对存储 Blob 数据读取器的访问权限。 详细了解[存储 Blob 数据读取器](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)。 

## <a name="train-with-datasets"></a>使用数据集进行训练

在机器学习试验中使用数据集来训练 ML 模型。 [详细了解如何使用数据集进行训练](how-to-train-with-datasets.md)

## <a name="next-steps"></a>后续步骤

* [使用 TabularDatasets 和自动化机器学习进行培训的分步示例](tutorial-first-experiment-automated-ml.md)。

* [为模型定型](how-to-train-ml-models.md)。

* 有关更多数据集训练示例，请参阅[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)。
