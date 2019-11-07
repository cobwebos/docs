---
title: 在门户中创建 Azure ML 工作区
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 门户中创建、查看和删除 Azure 机器学习工作区。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 9e3915a2f55680fc2e24ac63d5e7170ada026c2e
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615179"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>在 Azure 门户中创建和管理 Azure 机器学习工作区
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，你将在[Azure 机器学习](overview-what-is-azure-ml.md)的 Azure 门户中创建、查看和删除[**Azure 机器学习工作区**](concept-workspace.md)。  门户是开始使用工作区的最简单方法，但随着需求的更改或自动化的要求增加，你还可以[使用通过使用](reference-azure-machine-learning-cli.md) [Python 代码](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)或[通过 VS Code 扩展](how-to-vscode-tools.md#get-started-with-azure-machine-learning-for-visual-studio-code)来创建和删除工作区。

## <a name="create-a-workspace"></a>创建工作区

必须有 Azure 订阅，才能创建工作区。 如果还没有 Azure 订阅，请在开始前创建免费帐户。 立即试用[Azure 机器学习免费版或付费版](https://aka.ms/AMLFree)。

1. 使用 Azure 订阅凭据登录到[Azure 门户](https://portal.azure.com/)。 

1. 在 Azure 门户的左上角，选择“+ 创建资源”。

      ![创建新资源](../../../includes/media/aml-create-in-portal/create-workspace.gif)

1. 使用搜索栏查找**机器学习**。

1. 选择**机器学习**。

1. 在**机器学习**窗格中，选择 "**创建**" 以开始。

1. 提供以下信息来配置新工作区：

   字段|说明 
   ---|---
   工作区名称 |输入用于标识工作区的唯一名称。 本示例使用 docs-ws。 名称在整个资源组中必须唯一。 使用易于记忆且区别于其他人所创建工作区的名称。  
   订阅 |选择要使用的 Azure 订阅。
   资源组 | 使用订阅中的现有资源组，或者输入一个名称以创建新的资源组。 资源组保存 Azure 解决方案的相关资源。 本示例使用 docs-aml。 
   位置 | 选择离你的用户和数据资源最近的位置来创建工作区。
   工作区版本 | 选择 "**基本**" 或 "**企业**"。  此工作区版本确定你将拥有访问和定价的功能。 了解有关[基本和企业版产品/服务](overview-what-is-azure-ml.md#sku)的详细信息。 

    ![配置工作区](media/how-to-manage-workspace/select-edition.png)

1. 完成工作区配置后，选择“创建”。 

   > [!Warning] 
   > 在云中创建工作区可能需要几分钟的时间。

   完成创建后，会显示部署成功消息。 
 
 1. 若要查看新工作区，请选择“转到资源”。

### <a name="download-a-configuration-file"></a>下载配置文件

1. 如果要创建[笔记本 VM](tutorial-1st-experiment-sdk-setup.md#azure)，请跳过此步骤。

1. 如果计划使用引用此工作区的本地环境中的代码，请从工作区的“概述”部分中选择“下载 config.json”。  

   ![下载 config.json](./media/how-to-manage-workspace/configure.png)
   
   使用 Python 脚本或 Jupyter Notebook 将此文件放入到目录结构中。 它可以位于同一目录（名为 *.azureml* 的子目录）中，也可以位于父目录中。 创建笔记本 VM 时，会将此文件添加到 VM 上的正确目录。

## <a name="upgrade"></a>升级到 Enterprise edition

你可以将工作区从 Basic 版本升级到 Enterprise edition，以利用增强功能，如低代码体验和增强的安全功能。

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 选择要升级的工作区。

1. 选择顶部或升级消息中的 "**升级**"。

    ![升级工作区](media/how-to-manage-workspace/upgrade.png)

1. 选择 "**确认更新**"。


> [!IMPORTANT]
> 不能将 Enterprise edition 工作区降级为基本版工作区。 

## <a name="view"></a>查找工作区

1. 在顶部搜索字段中，键入**机器学习**。  

1. 选择**机器学习**。

   ![搜索 Azure 机器学习工作区](media/how-to-manage-workspace/find-workspaces.png)

1. 浏览筛选出的工作区列表。 筛选依据可包括订阅、资源组和位置。  

1. 选择要显示其属性的工作区。

## <a name="delete-a-workspace"></a>创建工作区

单击要删除的工作区顶部的“删除”按钮。

  ![“删除”按钮](media/how-to-manage-workspace/delete-workspace.png)


## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

按照全长教程，了解如何使用工作区生成、定型和部署具有 Azure 机器学习的模型。

> [!div class="nextstepaction"]
> [教程：定型模型](tutorial-train-models-with-aml.md)
