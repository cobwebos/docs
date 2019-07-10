---
title: 在资源管理器模板部署中集成 Azure Key Vault | Microsoft Docs
description: 了解如何在资源管理器模板部署期间使用 Azure Key Vault 来传递安全参数值
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 05/23/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: b91a3488750795e8ee9df6602bb2b3df8a9b08ec
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67436571"
---
# <a name="tutorial-integrate-azure-key-vault-in-your-resource-manager-template-deployment"></a>教程：在你的资源管理器模板部署中集成 Azure 密钥保管库

了解部署 Azure 资源管理器时如何从 Azure 密钥保管库检索密钥并将密钥作为参数传递。 该参数值永远不会公开，因为只会引用其密钥保管库 ID。 有关详细信息，请参阅[在部署过程中使用 Azure 密钥保管库传递安全参数值](./resource-manager-keyvault-parameter.md)。

在[设置资源部署顺序](./resource-manager-tutorial-create-templates-with-dependent-resources.md)教程中，你需要创建虚拟机 (VM)。 需提供 VM 管理员用户名和密码。 可以不提供密码，而是将密码预先存储在 Azure 密钥保管库中，然后自定义模板，以便在部署过程中从密钥保管库检索密码。

![显示具有密钥保管库的资源管理器模板的集成的关系图](./media/resource-manager-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

本教程涵盖以下任务：

> [!div class="checklist"]
> * 准备 Key Vault
> * 打开快速入门模板
> * 编辑参数文件
> * 部署模板
> * 验证部署
> * 清理资源

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先决条件

若要完成本文，需要做好以下准备：

* 包含[资源管理器工具扩展](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites) [Visual Studio Code](https://code.visualstudio.com/)。
* 若要增强安全性，请使用为 VM 管理员帐户生成的密码。 以下是密码生成示例：

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    验证生成的密码是否符合 VM 密码要求。 每个 Azure 服务具有特定的密码要求。 有关 VM 密码要求，请参阅[创建 VM 时，密码有什么要求？](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)。

## <a name="prepare-a-key-vault"></a>准备 Key Vault

在此部分，创建一个密钥保管库，然后向该密钥保管库添加密钥，这样就可以在部署模板时检索该密钥。 可以通过许多方法来创建密钥保管库。 在本教程中，我们使用 Azure PowerShell 来部署[资源管理器模板](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json)。 此模板执行以下操作：

* 创建启用了 `enabledForTemplateDeployment` 属性的密钥保管库。 此属性必须为 true，这样模板部署过程才能访问此密钥保管库中定义的机密  。
* 将密钥添加到密钥保管库。 该密钥存储 VM 管理员密码。

> [!NOTE]
> 如果你（作为要部署虚拟机模板的用户）不是密钥保管库的所有者或参与者，则密钥保管库的所有者或参与者必须向你授予对密钥保管库的 Microsoft.KeyVault/vaults/deploy/action 的访问权限  。 有关详细信息，请参阅[在部署过程中使用 Azure 密钥保管库传递安全参数值](./resource-manager-keyvault-parameter.md)。

若要运行以下 Azure PowerShell 脚本，请选择“试用”以打开 Azure Cloud Shell  。 若要粘贴脚本，请右键单击 shell 窗格，然后选择“粘贴”  。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your user principal name (email address) used to sign in to Azure"
$secretValue = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$keyVaultName = $projectName
$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -keyVaultName $keyVaultName -adUserId $adUserId -secretValue $secretValue
```

> [!IMPORTANT]
> * 资源组名称是追加了“rg”的项目名称  。 为了方便[清理本教程创建的资源](#clean-up-resources)，请在[部署下一模板](#deploy-the-template)时使用相同的项目名称和资源组名称。
> * 密钥的默认名称为 vmAdminPassword  。 该名称已在模板中硬编码。
> * 必须为密钥保管库启用名为“启用对 Azure 资源管理器的访问以部署模板”的访问策略，然后模板才能检索机密。 在模板中启用此策略。 有关此访问策略的详细信息，请参阅[部署密钥保管库和机密](./resource-manager-keyvault-parameter.md#deploy-key-vaults-and-secrets)。

模板有一个名为 keyVaultId 的输出值  。 在部署虚拟机时，请记下 ID 值以供将来使用。 资源 ID 格式为：

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

复制并粘贴 ID 时，此 ID 可能会拆分成多个行。 合并这些行并裁剪掉额外的空格。

若要对部署进行验证，请在同一 shell 窗格中运行以下 PowerShell 命令，以明文形式检索机密。 此命令只能在同一 shell 会话中使用，因为它使用在先前 PowerShell 脚本中定义的变量 $keyVaultName  。

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

现在已准备好密钥保管库和密钥。 以下部分显示如何自定义现有模板，以便在部署过程中检索机密。

## <a name="open-a-quickstart-template"></a>打开快速入门模板

Azure 快速入门模板是资源管理器模板的存储库。 无需从头开始创建模板，只需找到一个示例模板并对其自定义即可。 本教程中使用的模板称为[部署简单的 Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)。

1. 在 Visual Studio Code 中，选择“文件” > “打开文件”。  

1. 在“文件名”框中粘贴以下 URL： 

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. 选择“打开”以打开该文件。  方案与以下教程中使用的方案相同[：使用依赖的资源创建 Azure 资源管理器模板](./resource-manager-tutorial-create-templates-with-dependent-resources.md)中使用的。
   该模板定义五个资源：

   * `Microsoft.Storage/storageAccounts`。 请参阅[模板参考](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)。
   * `Microsoft.Network/publicIPAddresses`。 请参阅[模板参考](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)。
   * `Microsoft.Network/virtualNetworks`。 请参阅[模板参考](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)。
   * `Microsoft.Network/networkInterfaces`。 请参阅[模板参考](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)。
   * `Microsoft.Compute/virtualMachines`。 请参阅[模板参考](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)。

   在自定义模板之前，不妨对其进行一些基本的了解。

1. 选择“文件” > “另存为”，将该文件的副本保存到名为 azuredeploy.json 的本地计算机。   

1. 重复步骤 1-3 打开以下 URL，然后将文件保存为 azuredeploy.parameters.json  。

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>编辑参数文件

无需对模板文件进行任何更改。

1. 在 Visual Studio Code 中打开 azuredeploy.parameters.json（如果尚未打开）  。
1. 将 `adminPassword` 参数更新为：

    ```json
    "adminPassword": {
        "reference": {
            "keyVault": {
            "id": "/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>"
            },
            "secretName": "vmAdminPassword"
        }
    },
    ```

    > [!IMPORTANT]
    > 将“id”值替换为你在上一过程中创建的密钥保管库的资源 ID  。

    ![集成密钥保管库和资源管理器模板虚拟机部署参数文件](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)

1. 更新以下值：

    * adminUsername  ：虚拟机管理员帐户的名称。
    * dnsLabelPrefix  ：为 dnsLabelPrefix 值命名。

    有关名称的示例，请参阅前面的图像。

1. 保存更改。

## <a name="deploy-the-template"></a>部署模板

按照[部署模板](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template)中的说明执行操作。 将 azuredeploy.json 和 azuredeploy.parameters.json 上传到 Cloud Shell，然后使用以下 PowerShell 脚本部署模板   ：

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

部署模板时，请使用密钥保管库中使用的同一资源组。 此方法使你更轻松地清理资源，因为你需要仅删除一个资源组，而不是两个资源组。

## <a name="validate-the-deployment"></a>验证部署

成功部署虚拟机后，使用密钥保管库中存储的密码来测试登录凭据。

1. 打开 [Azure 门户](https://portal.azure.com)。

1. 选择“资源组” > \<YourResourceGroupName> > simpleWinVM    。
1. 选择顶部的“连接”  。
1. 选择“下载 RDP 文件”，然后遵照说明使用密钥保管库中存储的密码登录到虚拟机  。

## <a name="clean-up-resources"></a>清理资源

不再需要 Azure 资源时，请通过删除资源组来清理已部署的资源。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>后续步骤

在本教程中，你已从 Azure 密钥保管库检索了一个密钥。 你然后使用模板部署中的密钥。 若要了解如何创建链接模板，请参阅：

> [!div class="nextstepaction"]
> [创建链接模板](./resource-manager-tutorial-create-linked-templates.md)
