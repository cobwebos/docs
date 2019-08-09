---
title: 创建和管理工作区
titleSuffix: Azure Machine Learning service
description: 了解如何在 Azure 门户中创建、查看和删除 Azure 机器学习服务工作区。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 05/10/2019
ms.custom: seodec18
ms.openlocfilehash: f93b74c57d45ddfc7b47beff7852d183a93df00e
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856154"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces"></a>创建和管理 Azure 机器学习服务工作区

本文将介绍如何在 Azure 门户中针对 [Azure 机器学习服务](overview-what-is-azure-ml.md)创建、查看和删除 [**Azure 机器学习服务工作区**](concept-workspace.md)。  门户是开始使用工作区的最简单方法, 但随着需求的更改或自动化的要求增加, 你还可以[使用通过使用](reference-azure-machine-learning-cli.md) [Python 代码](https://aka.ms/aml-sdk)或[通过 VS Code 扩展](how-to-vscode-tools.md#get-started-with-azure-machine-learning)来创建和删除工作区。

## <a name="create-a-workspace"></a>创建工作区

必须有 Azure 订阅，才能创建工作区。 如果还没有 Azure 订阅，请在开始前创建免费帐户。 立即试用 [Azure 机器学习服务免费版或付费版](https://aka.ms/AMLFree)。

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="view"></a>查看工作区

1. 选择门户左上角的“所有服务”。

1. 在 "**所有服务**" 筛选器字段中, 键入 "**机器学习服务**"。  

1. 选择**机器学习服务工作区**。

   ![搜索 Azure 机器学习服务工作区](media/how-to-manage-workspace/all-services.png)

1. 浏览筛选出的工作区列表。 筛选依据可包括订阅、资源组和位置。  

1. 选择要显示其属性的工作区。
   ![工作区属性](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>创建工作区

单击要删除的工作区顶部的“删除”按钮。

  ![“删除”按钮](media/how-to-manage-workspace/delete-workspace.png)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

学习整个教程，了解如何通过 Azure 机器学习服务使用工作区来生成、定型和部署模型。

> [!div class="nextstepaction"]
> [教程：训练模型](tutorial-train-models-with-aml.md)
