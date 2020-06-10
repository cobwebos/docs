---
title: 教程 - 创建 Azure ML 工作区 - 资源管理器模板
description: 在本教程中，你将使用 Azure 资源管理器模板快速部署用于机器学习的 Azure 工作区
services: machine-learning
author: lobrien
ms.author: laobri
ms.custom: subject-armqs
ms.date: 05/26/2020
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.openlocfilehash: 76f37beb22e28c0232efd0d62e82c8d3b60c78dc
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/03/2020
ms.locfileid: "84345081"
---
# <a name="tutorial-deploy-an-azure-machine-learning-workspace-using-a-resource-manager-template"></a>教程：使用资源管理器模板部署 Azure 机器学习工作区
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本教程演示如何使用 Azure 资源管理器模板创建 Azure 机器学习工作区。 Azure 机器学习工作区可将你的所有机器学习资产从基线数据集组织到已部署的模型中。 工作区是一个单一位置，可供你与同事合作创建、运行和查看试验，管理训练和推理计算资源，以及对部署的模型进行监视和版本控制。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个[免费帐户](https://aka.ms/AMLFree)

* 若要从本地环境使用本文档中的 CLI 命令，需要使用 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="create-a-workspace"></a>创建工作区

### <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-machine-learning-create/)。

:::code language="json" source="~/quickstart-templates/101-machine-learning-create/azuredeploy.json" range="1-258" highlight="224-254":::

该模板中定义了以下资源：

* [Microsoft.MachineLearningServices/workspaces](/azure/templates/microsoft.machinelearningservices/workspaces)：创建 Azure ML 工作区。 在此模板中，位置和名称是用户可以传入或以交互方式输入的参数。

### <a name="deploy-the-template"></a>部署模板 

若要通过 Azure CLI 使用该模板，请登录并选择你的订阅（请参阅[使用 Azure CLI 登录](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)）。 然后运行：

```azurecli-interactive
read -p "Enter a project name that is used for generating resource names:" projectName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-machine-learning-create/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
workspaceName="${projectName}ws" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri $templateUri --parameters workspaceName=$workspaceName location=$location && 
echo "Press [ENTER] to continue ..." &&
read
```

运行上述命令时，请输入：

1. 项目名称，它将作为创建的资源组和 Azure ML 工作区名称的基础
1. 要在其中进行部署的 Azure 位置

## <a name="review-deployed-resources"></a>查看已部署的资源

若要查看 Azure ML 工作区，请执行以下操作：

1. 转到 https://portal.azure.com 
1. 登录 
1. 选择刚创建的工作区

你将看到 Azure 机器学习主页： 

:::image type="content" source="media/tutorial-resource-manager-workspace/workspace-home.png" alt-text="Azure ML 工作区的屏幕截图":::

若要查看与部署相关的所有资源，请单击左上方带有工作区名称（在屏幕截图中为 `my_templated_ws`）的链接。 该链接会转到 Azure 门户中的资源组。 资源组的名称为 `{projectName}rg`，工作区的名称为 `{projectName}ws`。

## <a name="clean-up-resources"></a>清理资源

如果不想使用此工作区，请删除它。 由于此工作区与其他资源（例如存储帐户）关联，因此可能需要删除创建的整个资源组。 可以使用门户删除资源组，方法是单击“删除”按钮并进行确认。 也可以从 CLI 中使用以下命令删除资源组： 

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>后续步骤

在本教程中，你通过 Azure 资源管理器模板创建了 Azure 机器学习工作区。 若要探索 Azure 机器学习，请继续学习以下教程。 

> [!div class="nextstepaction"]
> [教程：开始使用 Python SDK 创建第一个 ML 试验](tutorial-1st-experiment-sdk-setup.md)
