---
title: PowerShell 模块
titleSuffix: ML Studio (classic) - Azure
description: 使用 PowerShell 创建和管理 Azure 机器学习工作室（经典）工作区、实验和 Web 服务等。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 04/25/2019
ms.openlocfilehash: 6afd222730a9864e0b8edd681b1ce919b03c3be8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79204284"
---
# <a name="powershell-modules-for-azure-machine-learning-studio-classic"></a>Azure 机器学习工作室（经典）PowerShell 模块

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

使用 PowerShell 模块，可采用编程方式管理工作室（经典）资源和资产，例如工作区、数据集和 Web 服务。

你可以使用以下三个 Powershell 模块与工作室（经典）资源进行交互：

* 于 2018 年发布的 [Azure PowerShell Az](#az-rm)，包含 AzureRM 的所有功能，尽管使用不同的 cmdlet 名称
* 2016 年发布的 [AzureRM](#az-rm) 已替换为 PowerShell Az
* 于 2016 年发布的 [Azure 机器学习 PowerShell（经典）](#classic)

虽然这些 PowerShell 模块有一些相似之处，但每个模块都是针对特定场景设计的。 本文介绍了 PowerShell 模块之间的差异，帮助你决定选择哪些模块。  

检查下面的[支持表](#support-table)，查看每个模块支持哪些资源。 

## <a name="azure-powershell-az-and-azurerm"></a><a name="az-rm"></a> Azure PowerShell Az 和 AzureRM

Az 现在是与 Azure 交互的 PowerShell 模块，它包含 AzureRM 以前的所有功能。 AzureRM 将继续接收 bug 修复，但它不会接收任何新的 cmdlet 或功能。  Az 和 AzureRM 都管理使用 Azure 资源管理器  部署模型部署的解决方案。 这些资源包括工作室（经典）工作区和工作室（经典）新的 Web 服务。 

PowerShell 经典版可与 Az 或 AzureRM 一起安装，以处理新的和经典的资源类型。 但是，不建议同时安装 Az 和 AzureRM。 若要在 Az 和 AzureRM 之间做出选择，Microsoft 建议在未来所有部署中使用 Az。  如需详细了解 Az 与 AzureRM 以及迁移路径信息，请参阅 [Azure PowerShell Az 简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)。

若要开始使用 Az，请按照 [Azure Az 的安装说明](https://docs.microsoft.com/powershell/azure/install-az-ps)操作。

## <a name="powershell-classic"></a><a name="classic"></a> PowerShell（经典）

借助工作室（经典）[PowerShell 经典模块](https://aka.ms/amlps)，可管理使用经典部署模型  部署的资源。 这些资源包括工作室（经典）用户资产、经典 Web 服务和经典 Web 服务终结点。

然而，Microsoft 建议为所有未来资源使用资源管理器部署模型，以简化资源的部署和管理。 若要了解有关部署模型的详细信息，请参阅 [Azure 资源管理器与经典部署](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)一文。

若要开始使用 PowerShell（经典），请从 GitHub 下载[发行包](https://github.com/hning86/azuremlps/releases)并按照[安装说明](https://github.com/hning86/azuremlps/blob/master/README.md)执行操作。 这些说明解释了如何取消阻止已下载/解压缩的 DLL，然后将它导入到 PowerShell 环境。

PowerShell 经典版可与 Az 或 AzureRM 一起安装，以处理新的和经典的资源类型。

## <a name="powershell-support-table"></a><a name="support-table"></a> PowerShell 支持表


| | **Az** |  **PowerShell 经典** |
| --- | --- | --- |
| 创建/删除工作区 | [资源管理器模板](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| 管理工作区承诺计划 | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| 管理工作区用户 |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| 管理 Web 服务 | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br>（新 Web 服务）|| [New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>（经典 Web 服务） |
| 管理 Web 服务终结点/密钥 |  [Get-AzMlWebServiceKey](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekey)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| 管理用户数据集/已训练模型| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| 管理用户体验 |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| 管理自定义模块 | | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>后续步骤
请参阅有关这些 PowerShell 模块的完整文档：
* [PowerShell 经典](https://aka.ms/amlps)
* [Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
