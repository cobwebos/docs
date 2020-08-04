---
title: 使用 Azure 资源管理器模板创建你的第一个函数
description: 使用 Azure 资源管理器模板（ARM 模板）创建由 HTTP 触发的简单无服务器函数并将其部署到 Azure。
ms.date: 3/5/2020
ms.topic: quickstart
ms.service: azure-functions
ms.custom: subject-armqs
ms.openlocfilehash: e409b3b64ac6bc39ba4cb58397506723a67a0de2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87081706"
---
# <a name="quickstart-create-and-deploy-azure-functions-resources-from-an-arm-template"></a>快速入门：使用 ARM 模板创建和部署 Azure Functions 资源

在本文中，你将使用 Azure 资源管理器模板（ARM 模板）来创建响应 HTTP 请求的函数。 

完成本快速入门会从你的 Azure 帐户中扣取最多几美分的费用。 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-function-app-create-dynamic%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

### <a name="azure-account"></a>Azure 帐户 

在开始之前，必须拥有一个包含有效订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/)。

### <a name="create-a-local-functions-project"></a>创建本地函数项目

本文要求在创建的 Azure 资源上运行本地函数代码项目。 如果不先创建要发布的项目，你将无法完成本文的部署部分。 

选择以下选项卡之一，单击链接，完成使用所选语言创建函数应用的部分：

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[在 Visual Studio Code 中创建本地函数项目](functions-create-first-function-vs-code.md#create-an-azure-functions-project)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[在 Visual Studio 中创建本地函数项目](functions-create-your-first-function-visual-studio.md#create-a-function-app-project)

# <a name="command-line"></a>[命令行](#tab/command-line)

[从命令行创建本地函数项目](functions-create-first-azure-function-azure-cli.md#create-a-local-function-project)

---

在本地创建项目后，创建在 Azure 中运行新函数所需的资源。 

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-function-app-create-dynamic/)。

:::code language="json" source="~/quickstart-templates/101-function-app-create-dynamic/azuredeploy.json" range="1-140" highlight="55-136":::

此模板会创建下面 4 个 Azure 资源：

+ [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)：创建 Functions 需要的 Azure 存储帐户。
+ [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)：为函数应用创建无服务器的消耗托管计划。
+ [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)：创建函数应用。
+ [microsoft.insights/components](/azure/templates/microsoft.insights/components)：创建用于监视的 Application Insights 实例。

## <a name="deploy-the-template"></a>部署模板

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
read -p "Enter a resource group name that is used for generating resource names:" resourceGroupName &&
read -p "Enter the location (like 'eastus' or 'northeurope'):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter a resource group name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (like 'eastus' or 'northeurope')"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```
---

## <a name="validate-the-deployment"></a>验证部署

接下来，通过将项目发布到 Azure 并调用函数的 HTTP 终结点，验证所创建的函数应用托管资源。

### <a name="publish-the-function-project-to-azure"></a>将函数项目发布到 Azure

按照以下步骤将项目发布到新的 Azure 资源：

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

在输出中，复制 HTTP 触发器的 URL。 用它来测试在 Azure 中运行的函数。 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. **在“解决方案资源管理器”** 中，右键单击该项目并选择“发布”。

1. 在“选择发布目标”中，依次选择“Azure Functions 消耗计划”和“选择现有”，然后选择“创建配置文件”   。

    :::image type="content" source="media/functions-create-first-function-arm/choose-publish-target-visual-studio.png" alt-text="选择现有发布目标":::

1. 选择“订阅”，展开资源组，选择函数应用，然后选择“确定” 。

1. 发布完成后，复制“站点 URL”。

    :::image type="content" source="media/functions-create-first-function-arm/publish-summary-site-url.png" alt-text="从发布摘要复制站点 URL":::

1. 追加路径 `/api/<FUNCTION_NAME>?name=Functions`，其中 `<FUNCTION_NAME>` 是函数的名称。 调用 HTTP 触发器函数的 URL 采用以下格式：

    `http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=Functions`

使用此 URL 来测试在 Azure 中运行的 HTTP 触发器函数。

# <a name="command-line"></a>[命令行](#tab/command-line)

若要将本地代码发布到 Azure 中的函数应用，请使用 `publish` 命令：

```cmd
func azure functionapp publish <FUNCTION_APP_NAME>
```

在本例中，将 `<FUNCTION_APP_NAME>` 替换为函数应用名称。 可能需要使用 `az login` 再次登录。 

在输出中，复制 HTTP 触发器的 URL。 用它来测试在 Azure 中运行的函数。

---

### <a name="invoke-the-function-on-azure"></a>在 Azure 上调用函数

将为 HTTP 请求复制的 URL 粘贴到浏览器的地址栏中，确保已将 `name` 查询字符串作为 `?name=Functions` 追加到此 URL 的末尾，然后执行请求。 

应该会看到如下所示的响应：

<pre>Hello Functions!</pre>

## <a name="clean-up-resources"></a>清理资源

若要继续执行下一步并添加 Azure 存储队列输出绑定，请保留到目前为止构建的所有资源。

否则，请使用以下命令删除资源组及其包含的所有资源，以免产生额外的费用。

```azurecli
az group delete --name <RESOURCE_GROUP_NAME>
```

将 `<RESOURCE_GROUP_NAME>` 替换为资源组的名称。

## <a name="next-steps"></a>后续步骤

你现已发布第一个函数，接下来将输出绑定添加到函数来了解详细信息。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

> [!div class="nextstepaction"]
> [连接到 Azure 存储队列](functions-add-output-binding-storage-queue-vs-code.md)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

> [!div class="nextstepaction"]
> [连接到 Azure 存储队列](functions-add-output-binding-storage-queue-vs.md)

# <a name="command-line"></a>[命令行](#tab/command-line)

> [!div class="nextstepaction"]
> [连接到 Azure 存储队列](functions-add-output-binding-storage-queue-cli.md)

---
