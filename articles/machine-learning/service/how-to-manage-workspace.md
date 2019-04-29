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
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: e65f739a9641181381205c7255d0472325e8055c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819574"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces"></a>创建和管理 Azure 机器学习服务工作区

本文将介绍如何在 Azure 门户中针对 [Azure 机器学习服务](overview-what-is-azure-ml.md)创建、查看和删除 [**Azure 机器学习服务工作区**](concept-azure-machine-learning-architecture.md#workspace)。  也可以[使用 CLI](reference-azure-machine-learning-cli.md) 或 [Python 代码](https://aka.ms/aml-sdk)创建和删除工作区。

## <a name="create-a-workspace"></a>创建工作区 

必须有 Azure 订阅，才能创建工作区。 如果还没有 Azure 订阅，请在开始前创建免费帐户。 立即试用 [Azure 机器学习服务免费版或付费版](https://aka.ms/AMLFree)。

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="view"></a>查看工作区

1. 选择门户左上角的“所有服务”。 

1. 在中**所有服务**筛选器字段中，键入**机器学习服务**。  

1. 选择**机器学习服务工作区**。

   ![Azure 机器学习服务工作区中搜索](media/how-to-manage-workspace/all-services.png)

1. 浏览筛选出的工作区列表。 筛选依据可包括订阅、资源组和位置。  

1. 选择工作区以显示其属性。
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
