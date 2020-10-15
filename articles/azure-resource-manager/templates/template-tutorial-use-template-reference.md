---
title: 使用模板参考
description: 使用 Azure 资源管理器模板参考来创建模板。
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 3709511fa8da0a40f4faf4ab2dac9505d69003ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86118505"
---
# <a name="tutorial-utilize-the-resource-manager-template-reference"></a>教程：利用资源管理器模板参考

了解如何查找模板架构信息，以及如何使用该信息创建 Azure 资源管理器 (ARM) 模板。

在本教程中，请使用 Azure 快速入门模板中提供的基础模板。 可以使用模板参考文档来自定义模板。

![资源管理器模板参考部署存储帐户](./media/template-tutorial-use-template-reference/resource-manager-template-tutorial-deploy-storage-account.png)

本教程涵盖以下任务：

> [!div class="checklist"]
> * 打开快速入门模板
> * 了解模板
> * 查找模板参考
> * 编辑模板
> * 部署模板

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要完成本文，需要做好以下准备：

* 包含资源管理器工具扩展的 Visual Studio Code。 请参阅[快速入门：使用 Visual Studio Code 创建 Azure 资源管理器模板](quickstart-create-templates-use-visual-studio-code.md)。

## <a name="open-a-quickstart-template"></a>打开快速入门模板

[Azure 快速入门模板](https://azure.microsoft.com/resources/templates/)是 ARM 模板的存储库。 无需从头开始创建模板，只需找到一个示例模板并对其自定义即可。 本快速入门中使用的模板称为[创建标准存储帐户](https://azure.microsoft.com/resources/templates/101-storage-account-create/)。 该模板定义 Azure 存储帐户资源。

1. 在 Visual Studio Code 中，选择“文件”>“打开文件”。 
1. 在“文件名”中粘贴以下 URL：

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

1. 选择“打开”以打开该文件。
1. 选择“文件”>“另存为”，将该文件作为 **azuredeploy.json** 保存到本地计算机。 

## <a name="understand-the-schema"></a>了解架构

1. 在 VS Code 中将模板折叠到根级别。 你使用最简单的结构，其中包含以下元素：

    ![资源管理器模板的最简单结构](./media/template-tutorial-use-template-reference/resource-manager-template-simplest-structure.png)

    * **$schema**：指定描述模板语言版本的 JSON 架构文件所在的位置。
    * **contentVersion**：为此元素指定任意值，以便记录模板中的重要更改。
    * **parameters**：指定执行部署以自定义资源部署时提供的值。
    * **variables**：指定在模板中用作 JSON 片段以简化模板语言表达式的值。
    * **resources**：指定已在资源组中部署或更新的资源类型。
    * **outputs**：指定部署后返回的值。

1. 展开“resources”。 已定义 `Microsoft.Storage/storageAccounts` 资源。 SKU 名称使用参数值。  此参数称为 **storageAccountType**。

    ![资源管理器模板存储帐户定义](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resource.png)

1. 展开 **parameters** 即可查看 **storageAccountType** 是如何定义的。 此参数有四个允许的值。 需找到其他允许的值，然后修改参数定义。

    ![资源管理器模板存储帐户资源 SKU](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus-old.png)

## <a name="find-the-template-reference"></a>查找模板参考

1. 浏览到 [Azure 模板参考](/azure/templates/)。
1. 在“按标题筛选”框中输入“存储帐户”，然后在“参考”>“存储”下选择第一批“存储帐户”。 

    ![资源管理器模板参考存储帐户](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts.png)

    资源提供程序通常有多个 API 版本：

    ![资源管理器模板参考存储帐户版本](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-versions.png)

1. 从左窗格的“存储”下选择“所有资源”。  此页列出了存储资源提供程序的资源类型和版本。 建议对模板中定义的资源类型使用最新的 API 版本。

    ![资源管理器模板参考存储帐户类别版本](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-types-versions.png)

1. 选择最新版的 **storageAccount** 资源类型。 撰写本文时，最新版为 **2019-06-01**。 请确保此版本与模板中用于存储帐户资源的版本相匹配。 如果更新 API 版本，请验证资源定义是否与模板参考匹配。

1. 此页列出了 storageAccount 资源类型的详细信息。  例如，它列出了允许用于 **Sku** 对象的值。 Sku 比你之前打开的快速入门模板中列出的更多。 可以自定义快速入门模板，使之包含所有可用的存储类型。

    ![资源管理器模板参考存储帐户 sku](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-skus.png)

## <a name="edit-the-template"></a>编辑模板

从 Visual Studio Code 添加其他存储帐户类型，如以下屏幕截图所示：

![资源管理器模板存储帐户资源](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus.png)

## <a name="deploy-the-template"></a>部署模板

1. 登录到 [Azure Cloud Shell](https://shell.azure.com)

1. 通过在左上角选择“PowerShell”或“Bash”（适用于 CLI）来选择你喜欢使用的环境 。  进行切换时，需重启 shell。

    ![Azure 门户 - Cloud Shell - 上传文件](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. 依次选择“上传/下载文件”、“上传”。  请参阅上面的屏幕截图。 选择在上一部分保存的文件。 上传文件后，可以使用 ls 命令和 cat 命令验证文件是否已成功上传。

1. 在 Cloud Shell 中运行以下命令。 选择用于显示 PowerShell 代码或 CLI 代码的选项卡。

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json" --parameters storageAccountType='Standard_RAGRS'
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -storageAccountType "Standard_RAGRS"
    ```

    ---

 部署模板时，请使用新添加的值（例如 Standard_RAGRS）指定 storageAccountType 参数。 如果使用原始快速入门模板，部署会失败，因为 Standard_RAGRS 不是允许的值。

## <a name="clean-up-resources"></a>清理资源

不再需要 Azure 资源时，请通过删除资源组来清理部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”  。
2. 在“按名称筛选”字段中输入资源组名称。
3. 选择资源组名称。  应会看到，该资源组中总共有六个资源。
4. 在顶部菜单中选择“删除资源组”。 

## <a name="next-steps"></a>后续步骤

本教程介绍了如何使用模板参考来自定义现有的模板。 若要了解如何创建多个存储帐户实例，请参阅：

> [!div class="nextstepaction"]
> [创建多个实例](./template-tutorial-create-multiple-instances.md)
