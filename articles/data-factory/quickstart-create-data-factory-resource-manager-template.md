---
title: 使用 Azure 资源管理器模板（ARM 模板）创建 Azure 数据工厂
description: 使用 Azure 资源管理器模板（ARM 模板）创建示例 Azure 数据工厂管道。
services: data-factory
ms.service: data-factory
tags: azure-resource-manager
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan, jingwang
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 07/16/2020
ms.openlocfilehash: eb423ebd354adeb8273755d34323b283d53eb8b5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "87283888"
---
# <a name="quickstart-create-an-azure-data-factory-using-arm-template"></a>快速入门：使用 ARM 模板创建 Azure 数据工厂

> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
> * [版本 1](v1/data-factory-build-your-first-pipeline-using-arm.md)
> * [当前版本](quickstart-create-data-factory-resource-manager-template.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）来创建 Azure 数据工厂。 在此数据工厂中创建的管道会将数据从 Azure Blob 存储中的一个文件夹**复制**到另一个文件夹。 有关如何使用 Azure 数据工厂**转换**数据的教程，请参阅[教程：使用 Spark 转换数据](transform-data-using-spark.md)。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

> [!NOTE]
> 本文不提供数据工厂服务的详细介绍。 有关 Azure 数据工厂服务的介绍，请参阅 [Azure 数据工厂简介](introduction.md)。

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-factory-v2-blob-to-blob-copy%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

### <a name="azure-subscription"></a>Azure 订阅

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。

### <a name="create-a-file"></a>创建文件

打开文本编辑器（如记事本），并创建包含以下内容的名为“emp.txt”的文件：

```emp.txt
John, Doe
Jane, Doe
```

将此文件保存在 C:\ADFv2QuickStartPSH 文件夹中。 （如果此文件夹不存在，则创建它。）

## <a name="review-template"></a>审阅模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-data-factory-v2-blob-to-blob-copy/)。

:::code language="json" source="~/quickstart-templates/101-data-factory-v2-blob-to-blob-copy/azuredeploy.json":::

该模板中定义了 Azure 资源：

- [Microsoft.Storage/storageAccounts](/azure/templates/Microsoft.Storage/storageAccounts)：定义存储帐户。
- [Microsoft.DataFactory/factories](/azure/templates/microsoft.datafactory/factories)：创建 Azure 数据工厂。
- [Microsoft.DataFactory/factories/linkedServices](/azure/templates/microsoft.datafactory/factories/linkedservices)：创建 Azure 数据工厂链接服务。
- [Microsoft.DataFactory/factories/datasets](/azure/templates/microsoft.datafactory/factories/datasets)：创建 Azure 数据工厂数据集。
- [Microsoft.DataFactory/factories/pipelines](/azure/templates/microsoft.datafactory/factories/pipelines)：创建 Azure 数据工厂管道。

可以在[快速入门模板库](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datafactory&pageNumber=1&sort=Popular)中找到更多 Azure 数据工厂模板示例。

## <a name="deploy-the-template"></a>部署模板

1. 选择下图登录到 Azure 并打开一个模板。 此模板创建 Azure 数据工厂帐户、存储帐户和 blob 容器。

    [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-factory-v2-blob-to-blob-copy%2Fazuredeploy.json)

2. 选择或输入以下值。

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-deploy-arm-template.png" alt-text="部署 ADF ARM 模板":::

    除非另有指定，否则请使用默认值创建 Azure 数据工厂资源：

    - 订阅：选择 Azure 订阅。
    - 资源组：选择“新建”，输入资源组的唯一名称，然后选择“确定”。
    - **区域**：选择一个位置。  例如“美国东部”。
    - 数据工厂名称：使用默认值。
    - 位置：使用默认值。
    - **存储帐户名称**：使用默认值。
    - Blob 容器：使用默认值。

## <a name="review-deployed-resources"></a>查看已部署的资源

1. 选择“转到资源组”。

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-go-to-resource-group.png" alt-text="部署 ADF ARM 模板":::

2.  验证是否已创建 Azure 数据工厂。
    1. Azure 数据工厂名称的格式为 - datafactory\<uniqueid\>。

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-sample.png" alt-text="部署 ADF ARM 模板":::

2. 验证是否已创建存储帐户。
    1. 存储帐户名称的格式为 - storage\<uniqueid\>。

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-storage-account.png" alt-text="部署 ADF ARM 模板":::

3. 选择创建的存储帐户，并选择“容器”。
    1. 在“容器”页上，选择创建的 blob 容器。
        1. blob 容器名称的格式为 - blob\<uniqueid\>。

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-blob-container.png" alt-text="部署 ADF ARM 模板":::

### <a name="upload-a-file"></a>上传文件

1. 在“容器”页上，选择“上传”。

2. 在右侧窗口，选择“文件”框，然后浏览到先前创建的“emp.txt”文件并进行选择。

3. 展开“高级”标题。

4. 在“上传到文件夹”框中，输入“输入”。

5. 选择“上传”按钮  。 应该会在列表中看到 **emp.txt** 文件和上传状态。

6. 选择“关闭”图标 (X) 以关闭“上传 Blob”页面  。

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-upload-blob-file.png" alt-text="部署 ADF ARM 模板":::

使容器页保持打开状态，因为你可以使用它在快速入门结束时验证输出。

### <a name="start-trigger"></a>启动触发器

1. 导航到“数据工厂”页，选择创建的数据工厂。 

2. 选择“创建者和监视器”磁贴。 

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-author-monitor-tile.png" alt-text="部署 ADF ARM 模板":::

2. 选择“创作”选项卡 :::image type="icon" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-author.png" border="false":::。

3. 选择创建的管道 - ArmtemplateSampleCopyPipeline。

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-pipelines.png" alt-text="部署 ADF ARM 模板":::

4. 选择“添加触发器” > “立即触发器”。

    触发器:::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-trigger-now.png" alt-text="":::

5. 在“管道运行”下的右窗格中，选择“确定”。

### <a name="monitor-the-pipeline"></a>监视管道

1. 选择“监视”选项卡 :::image type="icon" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-monitor.png" border="false":::。

2. 此时会看到与管道运行相关联的活动运行。 在本快速入门中，管道只有一个活动，其类型为：“复制”。 因此会看到该活动运行。

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-successful-run.png" alt-text="部署 ADF ARM 模板":::

### <a name="verify-the-output-file"></a>验证输出文件

该管道自动在 blob 容器中创建一个输出文件夹。 然后将 emp.txt 文件从 input 文件夹复制到 output 文件夹。 

1. 在 Azure 门户的“容器”页中选择“刷新”，以查看输出文件夹。 

2. 在文件夹列表中，选择“output”。

3. 确认 **emp.txt** 已复制到 output 文件夹。 

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-output.png" alt-text="部署 ADF ARM 模板":::

## <a name="clean-up-resources"></a>清理资源

可以通过两种方式清理在快速入门中创建的资源。 可以[删除 Azure 资源组](../azure-resource-manager/management/delete-resource-group.md)，其中包括资源组中的所有资源。 若要使其他资源保持原封不动，请仅删除在此教程中创建的数据工厂。

删除资源组时会删除所有资源，包括其中的数据工厂。 运行以下命令可以删除整个资源组： 

```azurepowershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

如果只需删除数据工厂，而不是整个资源组，请运行以下命令： 

```azurepowershell-interactive
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你使用 ARM 模板创建了 Azure 数据工厂工作区，并验证了部署。 若要详细了解 Azure 数据工厂和 Azure 资源管理器，请继续阅读以下文章。

- [Azure 数据工厂文档](index.yml)
- 了解有关 [Azure 资源管理器](../azure-resource-manager/management/overview.md)的详细信息
- 获取其他 [Azure 数据工厂 ARM 模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datafactory&pageNumber=1&sort=Popular)