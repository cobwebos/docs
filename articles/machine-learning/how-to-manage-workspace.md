---
title: 在门户中创建工作区
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 门户中创建、查看和删除 Azure 机器学习工作区。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 09/22/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 38784b006acac4c3ff70b2aa3c38648e939eddeb
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90889910"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>在 Azure 门户中创建并管理 Azure 机器学习工作区


本文将介绍如何在 Azure 门户中针对 [Azure 机器学习](overview-what-is-azure-ml.md)创建、查看和删除 [Azure 机器学习工作区****](concept-workspace.md)。  门户是开始使用工作区的最简单方法，但随着需求的变化或自动化要求的增加，还可以[使用 CLI](reference-azure-machine-learning-cli.md)、[使用 Python 代码](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)或[使用 VS Code 扩展](tutorial-setup-vscode-extension.md)创建和删除工作区。

## <a name="create-a-workspace"></a>创建工作区

必须有 Azure 订阅，才能创建工作区。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

1. 使用 Azure 订阅的凭据登录到 [Azure 门户](https://portal.azure.com/)。 

1. 在 Azure 门户的左上角，选择“+ 创建资源”****。

      ![创建新资源](./media/how-to-manage-workspace/create-workspace.gif)

1. 使用搜索栏查找“机器学习”****。

1. 选择“机器学习”****。

1. 在“机器学习”窗格中，选择“创建”以开始**** ****。

1. 提供以下信息来配置新工作区：

   字段|说明 
   ---|---
   工作区名称 |输入用于标识工作区的唯一名称。 本示例使用 docs-ws****。 名称在整个资源组中必须唯一。 使用易于记忆且区别于其他人所创建工作区的名称。 工作区名称不区分大小写。
   订阅 |选择要使用的 Azure 订阅。
   资源组 | 使用订阅中的现有资源组，或者输入一个名称以创建新的资源组。 资源组保存 Azure 解决方案的相关资源。 本示例使用 docs-aml****。 你需要 *参与者* 或 *所有者* 角色才能使用现有的资源组。  有关访问权限的详细信息，请参阅 [管理对 Azure 机器学习工作区的访问](how-to-assign-roles.md)。
   位置 | 选择离你的用户和数据资源最近的位置来创建工作区。
   位置 | 选择离你的用户和数据资源最近的位置来创建工作区。

    ![配置工作区](./media/how-to-manage-workspace/select-edition.png)

1. 完成工作区配置后，选择“查看 + 创建”****。
2. 查看设置并进行任何其他更改或更正。 如果对设置感到满意，请选择“创建”****。

   > [!Warning] 
   > 在云中创建工作区可能需要几分钟时间。

   完成创建后，会显示部署成功消息。 
 
 1. 若要查看新工作区，请选择“转到资源”****。

### <a name="download-a-configuration-file"></a>下载配置文件

1. 如果要创建[计算实例](tutorial-1st-experiment-sdk-setup.md#azure)，请跳过此步骤。

1. 如果计划使用引用此工作区的本地环境中的代码，请**** 从工作区的“概述”**** 部分中选择“下载 config.json”。  

   ![下载 config.json](./media/how-to-manage-workspace/configure.png)
   
   使用 Python 脚本或 Jupyter Notebook 将此文件放入到目录结构中。 它可以位于同一目录（名为 *.azureml* 的子目录）中，也可以位于父目录中。 创建计算实例时，此文件会添加到 VM 上的正确目录中。
## <a name="find-a-workspace"></a><a name="view"></a>查找工作区

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 在顶部搜索字段中，键入“机器学习”。****  

1. 选择“机器学习”****。

   ![搜索 Azure 机器学习工作区](./media/how-to-manage-workspace/find-workspaces.png)

1. 浏览筛选出的工作区列表。 筛选依据可包括订阅、资源组和位置。  

1. 选择工作区即可显示其属性。

## <a name="delete-a-workspace"></a>创建工作区

在 [Azure 门户](https://portal.azure.com/)中，选择要删除的工作区顶部的“删除”****。

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="删除工作区":::

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>故障排除

### <a name="resource-provider-errors"></a>资源提供程序错误

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>移动工作区

> [!WARNING]
> 不支持将 Azure 机器学习工作区移动到另一个订阅，或将拥有的订阅移到新租户。 这样做可能会导致错误。

### <a name="deleting-the-azure-container-registry"></a>删除 Azure 容器注册表

Azure 机器学习工作区使用 Azure 容器注册表 (ACR) 执行某些操作。 首次需要 ACR 实例时，它会自动创建一个。

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>后续步骤

学习整个教程，了解如何通过 Azure 机器学习使用工作区来生成、定型和部署模型。

> [!div class="nextstepaction"]
> [教程：训练模型](tutorial-train-models-with-aml.md)
