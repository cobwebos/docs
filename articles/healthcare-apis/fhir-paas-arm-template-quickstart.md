---
title: 快速入门：使用 ARM 模板部署 Azure API for FHIR
description: 本快速入门介绍如何使用 Azure 资源管理器模板部署适用于快速医疗保健互操作性资源 (FHIR®) 的 Azure API。
author: mgblythe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mblythe
ms.date: 10/06/2020
ms.openlocfilehash: e9b2ec017ca985c1de739ee74c4d0732f39bdc1d
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91772730"
---
# <a name="quickstart-use-an-arm-template-to-deploy-azure-api-for-fhir"></a>快速入门：使用 ARM 模板部署 Azure API for FHIR

本快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）部署适用于快速医疗保健互操作性资源 (FHIR®) 的 Azure API。 可以通过 Azure 门户、PowerShell 或 CLI 部署 Azure API for FHIR。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮****。 登录后，该模板将在 Azure 门户中打开。

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="使用 Azure 门户中的 ARM 模板将 Azure API for FHIR 服务部署到 Azure。":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-api-for-fhir%2fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

# <a name="portal"></a>[Portal](#tab/azure-portal)

具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/)。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/)。
* 若要在本地运行代码，请安装 [Azure PowerShell](/powershell/azure/install-az-ps)。

# <a name="cli"></a>[CLI](#tab/CLI)

* 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/)。
* 若要在本地运行代码，请安装：
    * Bash shell（例如 [Git for Windows](https://gitforwindows.org) 中包含的 Git Bash）。
    * [Azure CLI](/cli/azure/install-azure-cli)。

---

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-azure-api-for-fhir/)。

:::code language="json" source="~/quickstart-templates/101-azure-api-for-fhir/azuredeploy.json":::

该模板定义了一个 Azure 资源：

* Microsoft.HealthcareApis/services

<!--

Replace the line above with the following line once https://docs.microsoft.com/azure/templates/microsoft.healthcareapis/services goes live:

* [**Microsoft.HealthcareApis/services**](/azure/templates/microsoft.healthcareapis/services)

-->

## <a name="deploy-the-template"></a>部署模板

# <a name="portal"></a>[Portal](#tab/azure-portal)

选择以下链接以使用 Azure 门户中的 ARM 模板部署 Azure API for FHIR：

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="使用 Azure 门户中的 ARM 模板将 Azure API for FHIR 服务部署到 Azure。":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-api-for-fhir%2fazuredeploy.json)

在“部署 Azure API for FHIR”页上：

1. 如果需要，可以将“订阅”从默认值更改为其他订阅。

2. 对于“资源组”，请选择“新建”，输入新资源组的名称，然后选择“确定”  。

3. 如果创建了新的资源组，请为该资源组选择一个区域。

4. 输入新的服务名称，然后选择 Azure API for FHIR 所在的位置。 该位置可以与资源组所在的区域相同，也可以与资源组所在的区域不同。

    :::image type="content" source="./media/fhir-paas-arm-template-quickstart/deploy-azure-api-fhir.png" alt-text="使用 Azure 门户中的 ARM 模板将 Azure API for FHIR 服务部署到 Azure。":::

5. 选择“查看 + 创建”  。

6. 阅读条款和条件，然后选择“创建”。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> 若要在本地运行 PowerShell 脚本，请首先输入 `Connect-AzAccount` 来设置 Azure 凭据。

如果还没有为订阅注册 `Microsoft.HealthcareApis` 资源提供程序，则可以使用以下交互式代码进行注册。 若要在 Azure Cloud Shell 中运行该代码，请在任何代码块的右上角选择“尝试”。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

使用以下代码通过 ARM 模板部署 Azure API for FHIR 服务。 该代码会提示你输入新的 FHIR 服务名称、新资源组的名称及其各自所在的位置。

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter a name for the new Azure API for FHIR service"
$serviceLocation = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion
Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure API for FHIR service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-api-for-fhir/azuredeploy.json `
    -serviceName $serviceName `
    -location $serviceLocation
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

如果还没有为订阅注册 `Microsoft.HealthcareApis` 资源提供程序，则可以使用以下交互式代码进行注册。 若要在 Azure Cloud Shell 中运行该代码，请在任何代码块的右上角选择“尝试”。

```azurecli-interactive
az extension add --name healthcareapis
```

使用以下代码通过 ARM 模板部署 Azure API for FHIR 服务。 该代码会提示你输入新的 FHIR 服务名称、新资源组的名称及其各自所在的位置。

```azurecli-interactive
read -p "Enter a name for the new Azure API for FHIR service: " serviceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " serviceLocation &&
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
params='serviceName='$serviceName' location='$serviceLocation &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure API for FHIR service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-api-for-fhir/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> 部署需要数分钟才能完成。 记下 Azure API for FHIR 服务和资源组的名称，稍后将使用它们来查看已部署的资源。

## <a name="review-deployed-resources"></a>查看已部署的资源

# <a name="portal"></a>[Portal](#tab/azure-portal)

按照以下步骤查看新 Azure API for FHIR 服务的概览：

1. 在 [Azure 门户](https://portal.azure.com)中，搜索并选择“Azure API for FHIR”。

2. 在 FHIR 列表中，选择你的新服务。 此时将显示新 Azure API for FHIR 服务的“概述”页。

3. 若要验证是否预配了新的 FHIR API 帐户，请选择“FHIR 元数据终结点”旁边的链接，以提取 FHIR API 功能语句。 该链接的格式为 `https://<service-name>.azurehealthcareapis.com/metadata`。 如果预配了帐户，则会显示一个较大的 JSON 文件。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

运行以下交互式代码来查看有关 Azure API for FHIR 服务的详细信息。 必须输入新服务和资源组的名称。

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter the name of your Azure API for FHIR service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned"

$requestUri="https://" + $serviceName + ".azurehealthcareapis.com/metadata"
$metadata = Invoke-WebRequest -Uri $requestUri
$metadata.RawContent
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

运行以下交互式代码来查看有关 Azure API for FHIR 服务的详细信息。 必须输入新服务和资源组的名称。

```azurecli-interactive
read -p "Enter the name of your Azure API for FHIR service: " serviceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az healthcareapis service show --resource-group $resourceGroupName --resource-name $serviceName" &&
az healthcareapis service show --resource-group $resourceGroupName --resource-name $serviceName &&
read -p "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned: " &&
requestUrl='https://'$serviceName'.azurehealthcareapis.com/metadata' &&
curl --url $requestUrl &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="clean-up-resources"></a>清理资源

如果不再需要该资源组，可以将其删除，这将删除资源组中的资源。

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 在 [Azure 门户](https://portal.azure.com)中，搜索并选择“资源组”。

2. 在资源组列表中，选择你的资源组的名称。

3. 在资源组的“概览”页中，选择“删除资源组” 。

4. 在确认对话框中，键入资源组的名称，然后选择“删除”。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to delete"
Write-Verbose "Remove-AzResourceGroup -Name $resourceGroupName" -Verbose
Remove-AzResourceGroup -Name $resourceGroupName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
read -p "Enter the name of the resource group to delete: " resourceGroupName &&
echo "DELETE A RESOURCE GROUP (AND ITS RESOURCES):  az group delete --name $resourceGroupName" &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="next-steps"></a>后续步骤

有关引导你完成 ARM 模板创建过程的分步教程，请参阅：

> [!div class="nextstepaction"]
> [教程：创建和部署你的第一个 ARM 模板](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
