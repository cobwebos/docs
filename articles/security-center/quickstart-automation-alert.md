---
title: 使用 Azure 资源管理器 (ARM) 模板为特定安全警报创建安全自动化
description: 了解如何通过使用 Azure 资源管理器 (ARM) 模板创建 Azure 安全中心自动化来触发逻辑应用，其中该应用将由特定的安全中心警报触发
services: azure-resource-manager
author: memildin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: memildin
ms.date: 08/20/2020
ms.openlocfilehash: 6c8a3d6c291435a379a637707a42f9ad7fe8dc00
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906357"
---
# <a name="quickstart-create-an-automatic-response-to-a-specific-security-alert-using-an-azure-resource-manager-template-arm-template"></a>快速入门：使用 Azure 资源管理器 (ARM) 模板创建特定安全警报自动响应

本快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）来创建工作流自动化，以便在 Azure 安全中心收到特定安全警报时触发逻辑应用。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-securitycenter-create-automation-for-alertnamecontains%2fazuredeploy.json)


## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

有关使用Azure 安全中心的工作流自动化功能所需的角色和权限列表，请参阅[工作流自动化](workflow-automation.md)。


## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-securitycenter-create-automation-for-alertnamecontains/)。

:::code language="json" source="~/quickstart-templates/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json":::

### <a name="relevant-resources"></a>相关资源

- [**Microsoft.Security/automations**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-securitycenter-create-automation-for-alertnamecontains/#microsoftsecurity-resource-provider)：在收到包含特殊字符串的 Azure 安全中心警报时将触发逻辑应用的自动化。
- [**Microsoft.Logic/workflows**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-securitycenter-create-automation-for-alertnamecontains/#microsoftlogic-resource-provider)：可触发的空逻辑应用。

有关其他安全中心快速入门模板，请参阅这些[社区提供的模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Security)。

## <a name="deploy-the-template"></a>部署模板

  - PowerShell：
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
    New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json
    ```

  - **CLI**：
    ```azurecli-interactive
    az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
    az group deployment create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json
    ```

  - **门户**：

    [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-securitycenter-create-automation-for-alertnamecontains%2fazuredeploy.json)

    若要查找有关此部署选项的详细信息，请参阅[使用部署按钮从 GitHub 存储库部署模板](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-to-azure-button)。

## <a name="review-deployed-resources"></a>查看已部署的资源

使用 Azure 门户检查是否已部署工作流自动化。 

1. 从 [Azure 门户](https://portal.azure.com)打开“安全中心”。
1. 在顶部菜单栏中，选择筛选器图标，然后选择在其上部署了新的工作流自动化的特定订阅。
1. 从安全中心的侧边栏打开“工作流自动化”并查看新的自动化。
    :::image type="content" source="./media/quickstart-automation-alert/validating-template-run.png" alt-text="已配置的自动化列表" lightbox="./media/quickstart-automation-alert/validating-template-run.png":::
    >[!TIP]
    > 如果订阅中有许多工作流自动化，则使用“按名称筛选”选项。 

## <a name="clean-up-resources"></a>清理资源

不再需要时，请在 Azure 门户中删除该工作流自动化。

1. 从 [Azure 门户](https://portal.azure.com)打开“安全中心”。
1. 在顶部菜单栏中，选择筛选器图标，然后选择在其上部署了新的工作流自动化的特定订阅。
1. 从安全中心的侧边栏打开“工作流自动化”并查找要删除的自动化。
    :::image type="content" source="./media/quickstart-automation-alert/deleting-workflow-automation.png" alt-text="删除工作流自动化的步骤" lightbox="./media/quickstart-automation-alert/deleting-workflow-automation.png":::
1. 选中要删除的项的复选框。
1. 从工具栏中选择“删除”。


## <a name="next-steps"></a>后续步骤

有关引导你完成模板创建过程的分步教程，请参阅：

> [!div class="nextstepaction"]
> [教程：创建和部署你的第一个 ARM 模板](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)