---
title: 教程 - 创建和部署模板
description: 创建第一个 Azure 资源管理器模板。 本教程将介绍模板文件语法，以及如何部署存储帐户。
author: mumian
ms.date: 05/20/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 2180ca80d87643eb885d814318e516b4b3c53f37
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714791"
---
# <a name="tutorial-create-and-deploy-your-first-arm-template"></a>教程：创建和部署第一个 ARM 模板

本教程介绍 Azure 资源管理器 (ARM) 模板。 其中将会说明如何创建初学者模板并将其部署到 Azure。 本教程还将介绍模板的结构，以及使用模板时所需的工具。 完成本教程大约需要 **12 分钟**，但实际时间根据需要安装的工具数量而异。

本文是相关教学系列的第一篇教程。 在学习该教学系列的过程中，你将逐步修改入门模板，直到了解了 ARM 模板的所有核心部分。 这些元素是更复杂的模板的构建基块。 我们希望在本教学系列结束时，你能够自信地创建自己的模板，并准备好使用这些模板自动完成部署。

若要了解使用模板的好处，以及为何要使用模板自动完成部署，请参阅 [Azure 资源管理器模板](overview.md)。

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="get-tools"></a>获取工具

首先，请确保已获取创建和部署模板所需的工具。

### <a name="editor"></a>编辑器

模板是一些 JSON 文件。 若要创建模板，需要一个好用的 JSON 编辑器。 我们建议使用带有资源管理器工具扩展的 Visual Studio Code。 如果需要安装这些工具，请参阅[使用 Visual Studio Code 创建 ARM 模板](use-vs-code-to-create-template.md)。

### <a name="command-line-deployment"></a>命令行部署

还需要使用 Azure PowerShell 或 Azure CLI 来部署模板。 如果使用 Azure CLI，则必须具有最新版本。 有关安装说明，请参阅：

- [安装 Azure PowerShell](/powershell/azure/install-az-ps)
- [在 Windows 上安装 Azure CLI](/cli/azure/install-azure-cli-windows)
- [在 Linux 上安装 Azure CLI](/cli/azure/install-azure-cli-linux)

安装 Azure PowerShell 或 Azure CLI 后，请务必完成首次登录。 有关帮助，请参阅[登录 - PowerShell](/powershell/azure/install-az-ps#sign-in) 或[登录 - Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in)。

好了，现在可以开始了解模板。

## <a name="create-your-first-template"></a>创建第一个模板

1. 打开装有资源管理器工具扩展的 Visual Studio Code。
1. 在“文件”菜单中，选择“新建文件”以创建新的文件。  
1. 在“文件”菜单中选择“另存为”。  
1. 将该文件命名为 **azuredeploy**，然后选择“JSON”文件扩展名。  完整的文件名为 **azuredeploy.json**。
1. 将该文件保存到工作站。 选择容易记住的路径，因为稍后在部署模板时需要提供该路径。
1. 将以下 JSON 内容复制并粘贴到该文件中：

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": []
    }
    ```

    VS Code 环境如下所示：

    ![资源管理器模板 - Visual Studio Code First 模板](./media/template-tutorial-create-first-template/resource-manager-visual-studio-code-first-template.png)

    此模板不部署任何资源。 我们从一个空白模板开始，以便可以熟悉模板的部署步骤，同时尽量减少出错的可能性。

    JSON 文件包含以下元素：

    - **$schema**：指定 JSON 架构文件的位置。 架构文件描述模板中可用的属性。 例如，架构定义 **resources** 作为模板的有效属性之一。 不要担心架构的日期为 2019-04-01。 此架构版本是最新的，其中包含所有最新功能。 之所以未更改架构日期，是因为自从推出以来，它尚未发生重大更改。
    - **contentVersion**：指定模板的版本（例如 1.0.0.0）。 可为此元素提供任意值。 使用此值记录模板中的重要更改。 使用模板部署资源时，此值可用于确保使用正确的模板。
    - **resources**：包含要部署或更新的资源。 此元素目前是空的，但稍后要添加资源。

1. 保存文件。

祝贺你，现已创建第一个模板。

## <a name="sign-in-to-azure"></a>登录 Azure

若要开始使用 Azure PowerShell/Azure CLI，请使用你的 Azure 凭据登录。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az login
```

---

如果你有多个 Azure 订阅，请选择要使用的订阅：

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---


## <a name="create-resource-group"></a>创建资源组

部署模板时，请指定一个包含资源的资源组。 在运行部署命令之前，请使用 Azure CLI 或 Azure PowerShell 创建该资源组。 使用以下代码部分中的选项卡在 Azure PowerShell 与 Azure CLI 之间进行选择。 本文中的 CLI 示例针对 Bash shell 编写。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-template"></a>部署模板

若要部署模板，请使用 Azure CLI 或 Azure PowerShell。 使用创建的资源组。 为部署指定一个适当的名称，以便可以在部署历史记录中轻松识别该部署。 为方便起见，另请创建一个用于存储模板文件路径的变量。 使用此变量可以更轻松地运行部署命令，因为无需在每次部署时重新键入路径。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
New-AzResourceGroupDeployment `
  -Name blanktemplate `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要运行此部署命令，必须具有 Azure CLI 的 [最新版本](/cli/azure/install-azure-cli)。

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az deployment group create \
  --name blanktemplate \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

部署命令将返回结果。 查看 `ProvisioningState` 以确定部署是否成功。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

![PowerShell 部署预配状态](./media/template-tutorial-create-first-template/resource-manager-deployment-provisioningstate.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

![Azure CLI 部署预配状态](./media/template-tutorial-create-first-template/azure-cli-provisioning-state.png)

---

> [!NOTE]
> 如果部署失败，请将 **debug** 开关和部署命令配合使用来显示调试日志。  还可以使用 **verbose** 开关来显示完整的调试日志。

## <a name="verify-deployment"></a>验证部署

可以通过在 Azure 门户中浏览资源组来验证部署。

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在左侧菜单中选择“资源组”。 

1. 选择在上一过程中部署的资源组。 默认名称为 **myResourceGroup**。 你应会看到，该资源组中未部署任何资源。

1. 请注意，在概述的右上角显示了部署状态。 选择“1 成功”。 

   ![查看部署状态](./media/template-tutorial-create-first-template/deployment-status.png)

1. 将会看到该资源组的部署历史记录。 选择“blanktemplate”。 

   ![选择部署](./media/template-tutorial-create-first-template/select-from-deployment-history.png)

1. 将会看到部署摘要。 在本例中，显示的信息不是很多，因此尚未部署任何资源。 在本教学系列的后续教程中，你可能会发现，查看部署历史记录中的摘要非常有用。 请注意，在左侧可以查看部署过程中使用的输入、输出和模板。

   ![查看部署摘要](./media/template-tutorial-create-first-template/view-deployment-summary.png)

## <a name="clean-up-resources"></a>清理资源

若要继续学习下一篇教程，则不需删除该资源组。

如果就此停止学习，请删除该资源组。

1. 在 Azure 门户上的左侧菜单中选择“资源组”  。
2. 在“按名称筛选”字段中输入资源组名称。 
3. 选择资源组名称。
4. 在顶部菜单中选择“删除资源组”。 

## <a name="next-steps"></a>后续步骤

现已创建一个要部署到 Azure 的简单模板。 在下一篇教程中，你要将一个存储帐户添加到该模板，然后将其部署到资源组。

> [!div class="nextstepaction"]
> [添加资源](template-tutorial-add-resource.md)
