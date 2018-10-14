---
title: 使用 Visual Studio Code 创建 Azure 资源管理器模板 | Microsoft Docs
description: 使用 Visual Studio Code 和可在资源管理器模板上运行的 Azure 资源管理器工具扩展。
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 09/07/2018
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: a2b4c4824960c21011876a7c0adf029fc56d93d2
ms.sourcegitcommit: 3150596c9d4a53d3650cc9254c107871ae0aab88
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2018
ms.locfileid: "47419111"
---
# <a name="quickstart-create-azure-resource-manager-templates-by-using-visual-studio-code"></a>快速入门：使用 Visual Studio Code 创建 Azure 资源管理器模板

了解如何使用 Visual Studio Code 和 Azure 资源管理器工具扩展创建 Azure 资源管理器模板。 可以在 Visual Studio Code 中不使用扩展创建资源管理器模板，但是该扩展提供自动完成选项，可以简化模板开发。 若要了解与部署和管理 Azure 解决方案相关联的概念，请参阅 [Azure 资源管理器概述](resource-group-overview.md)。

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要完成本文，需要做好以下准备：

- [Visual Studio Code](https://code.visualstudio.com/)。
- 资源管理器工具扩展。 若要安装此扩展，请使用以下步骤：

    1. 打开 Visual Studio Code。
    2. 按 **CTRL+SHIFT+X** 打开“扩展”窗格
    3. 搜索“Azure 资源管理器工具”，然后选择“安装”。
    4. 选择“重新加载”完成扩展安装。

## <a name="open-a-quickstart-template"></a>打开快速入门模板

无需从头开始创建模板，可以通过 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/)打开一个模板。 Azure 快速入门模板是资源管理器模板的存储库。

本快速入门中使用的模板称为[创建标准存储帐户](https://azure.microsoft.com/resources/templates/101-storage-account-create/)。 该模板定义 Azure 存储帐户资源。

1. 在 Visual Studio Code 中，选择“文件”>“打开文件”。
2. 在“文件名”中粘贴以下 URL：

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. 选择“打开”以打开该文件。
4. 选择“文件”>“另存为”，将该文件作为 **azuredeploy.json** 保存到本地计算机。

## <a name="edit-the-template"></a>编辑模板

若要了解如何使用 Visual Studio Code 编辑模板，请将额外的一个元素添加到 outputs 节。

1. 在 Visual Studio Code 中，将额外的一个输出添加到导出的模板：

    ```json
    "storageUri": {
      "type": "string",
      "value": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
    }
    ```

    完成后，outputs 节如下所示：

    ```json
    "outputs": {
      "storageAccountName": {
        "type": "string",
        "value": "[variables('storageAccountName')]"
      },
      "storageUri": {
        "type": "string",
        "value": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
      }
    }
    ```

    如果在 Visual Studio Code 中复制并粘贴了代码，请尝试重新键入 **value** 元素，以体验资源管理器工具扩展的 intellisense 功能。

    ![资源管理器模板 - Visual Studio Code - intellisense](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/resource-manager-templates-visual-studio-code-intellisense.png)

2. 选择“文件”>“保存”以保存文件。

## <a name="deploy-the-template"></a>部署模板

可通过多种方法来部署模板。  本快速入门从 Azure 门户使用 Azure Cloud Shell。 Cloud Shell 支持 Azure CLI 和 Azure PowerShell。 

1. 登录到 [Azure 门户](https://portal.azure.com)
2. 如下图所示，选择右上角的“Cloud Shell”：

    ![Azure 门户 - Cloud Shell](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell.png)

    Cloud Shell 在窗口底部打开。

3. 在 Cloud Shell 的左上角，它会显示 **PowerShell** 或 **Bash**。 若要使用 CLI，需打开一个 Bash 会话。 若要运行 PowerShell，需打开一个 PowerShell 会话。 选择向下箭头可在 Bash 与 PowerShell 之间切换。 下图显示如何从 PowerShell 切换到 Bash。

    ![Azure 门户 - Cloud Shell - CLI](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-cli.png)

    进行切换时，需重启 shell。
4. 依次选择“上传/下载文件”、“上传”。

    # <a name="clitabcli"></a>[CLI](#tab/CLI)

    ![Azure 门户 - Cloud Shell - 上传文件](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file.png)
   
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)
    
    ![Azure 门户 - Cloud Shell - 上传文件](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file-powershell.png)
    
    ---

    必须先上传模板文件，然后才能通过 shell 来部署它。
5. 选择前面在本快速入门中保存的文件。 默认名称为 **azuredeploy.json**。
6. 在 Cloud Shell 中，运行 **ls** 命令来验证是否已成功上传文件。 还可以使用 **cat** 命令来验证模板内容。 下图显示如何从 Bash 运行命令。  使用的命令与 PowerShell 会话中的命令相同。

    # <a name="clitabcli"></a>[CLI](#tab/CLI)

    ![Azure 门户 - Cloud Shell - 列出文件](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file.png)
   
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)
    
    ![Azure 门户 - Cloud Shell - 列出文件](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file-powershell.png)
    
    ---
7. 在 Cloud Shell 中运行以下命令。 选择用于显示 PowerShell 代码或 CLI 代码的选项卡。

    # <a name="clitabcli"></a>[CLI](#tab/CLI)
    ```cli
    az group create --name <ResourceGroupName> --location <AzureLocation>

    az group deployment create --name <DeploymentName> --resource-group <ResourceGroupName> --template-file <TemplateFileName>
    ```
   
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)
    
    ```powershell
    New-AzureRmResourceGroup -Name <ResourceGroupName> -Location <AzureLocation>

    New-AzureRmResourceGroupDeployment -ResourceGroupName <ResourceGroupName> -TemplateFile <TemplateFileName>
    ```
    
    ---

    以下屏幕截图显示了一个示例部署：

    # <a name="clitabcli"></a>[CLI](#tab/CLI)

    ![Azure 门户 - Cloud Shell - 部署模板](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template.png)
   
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)
    
    ![Azure 门户 - Cloud Shell - 部署模板](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template-powershell.png)
    
    ---

    屏幕截图中使用了以下值：

    - **&lt;ResourceGroupName>**：myresourcegroup0709。 参数有两种形式。  请确保使用相同的值。
    - **&lt;AzureLocation>**：eastus2
    - **&lt;DeployName>**：mydeployment0709
    - **&lt;TemplateFile>**：azuredeploy.json

    在屏幕截图上的输出中，存储帐户名称为 *3tqebj3slyfyestandardsa*。 

7. 运行以下 CLI 或 PowerShell 命令，列出新建的存储帐户：

    # <a name="clitabcli"></a>[CLI](#tab/CLI)
    ```cli
    az storage account show --resource-group <ResourceGroupName> --name <StorageAccountName>
    ```
   
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)
    
    ```powershell
    Get-AzureRmStorageAccount -ResourceGroupName <ResourceGroupName> -Name <StorageAccountName>
    ```
    
    ---

## <a name="clean-up-resources"></a>清理资源

不再需要 Azure 资源时，请通过删除资源组来清理部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”。
2. 在“按名称筛选”字段中输入资源组名称。
3. 选择资源组名称。  应会看到，该资源组中总共有六个资源。
4. 在顶部菜单中选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

本快速入门的主要关注点是如何使用 Visual Studio Code 来编辑 Azure 快速入门模板中的现有模板。 此外还介绍了如何使用 Azure Cloud Shell 中的 CLI 或 PowerShell 来部署模板。 Azure 快速入门模板中的模板可能并未提供你所需的一切。 下一教程介绍如何从模板参考中查找信息，以便创建加密的 Azure 存储帐户。

> [!div class="nextstepaction"]
> [创建加密的存储帐户](./resource-manager-tutorial-create-encrypted-storage-accounts.md)
