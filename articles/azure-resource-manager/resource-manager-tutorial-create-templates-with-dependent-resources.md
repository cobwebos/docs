---
title: 使用依赖的资源创建 Azure 资源管理器模板 | Microsoft Docs
description: 了解如何使用多个资源创建 Azure 资源管理器模板，以及如何使用 Azure 门户部署该模板
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/09/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 50f1c81f08787181de2fe3a9f6fb97a96a2bd882
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114306"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>教程：使用依赖的资源创建 Azure 资源管理器模板

了解如何创建 Azure 资源管理器模板，以便部署多个资源。  创建模板以后，请通过 Azure 门户使用 Cloud Shell 部署该模板。

本教程介绍如何创建存储帐户、虚拟机、虚拟网络以及一些其他的依赖资源。 某些资源的部署依赖于另一资源的存在。 例如，创建虚拟机的前提是其存储帐户和网络接口存在。 可通过将一个资源标记为依赖于其他资源来定义此关系。 Resource Manager 将评估资源之间的依赖关系，并根据其依赖顺序进行部署。 如果资源互不依赖，资源管理器将以并行方式部署资源。 有关详细信息，请参阅[定义 Azure 资源管理器模板中部署资源的顺序](./resource-group-define-dependencies.md)。

本教程涵盖以下任务：

> [!div class="checklist"]
> * 准备 Key Vault
> * 打开快速入门模板
> * 浏览模板
> * 编辑参数文件
> * 部署模板

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要完成本文，需要做好以下准备：

* 包含资源管理器工具扩展的 [Visual Studio Code](https://code.visualstudio.com/)。  请参阅[安装扩展](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)

## <a name="prepare-key-vault"></a>准备 Key Vault

为防止密码喷洒攻击，建议对虚拟机管理员帐户使用自动生成的密码，并使用 Key Vault 来存储密码。 以下过程创建 Key Vault 以及用于存储密码的机密。 它还配置模板部署访问存储在 Key Vault 中的机密时所需的权限。 如果 Key Vault 属于其他 Azure 订阅，则需要其他访问策略。 有关详细信息，请参阅[在部署过程中使用 Azure Key Vault 传递安全参数值](./resource-manager-keyvault-parameter.md)。

1. 登录到 [Azure Cloud Shell](https://shell.azure.com)。
2. 从左上角切换到最常用的环境，即 **PowerShell** 或 **Bash**。
3. 运行以下 Azure PowerShell 或 Azure CLI 命令。  

    ```azurecli-interactive
    keyVaultName='<your-unique-vault-name>'
    resourceGroupName='<your-resource-group-name>'
    location='Central US'
    userPrincipalName='<your-email-address-associated-with-your-subscription>'
    
    # Create a resource group
    az group create --name $resourceGroupName --location $location
    
    # Create a Key Vault
    keyVault=$(az keyvault create \
      --name $keyVaultName \
      --resource-group $resourceGroupName \
      --location $location \
      --enabled-for-template-deployment true)
    keyVaultId=$(echo $keyVault | jq -r '.id')
    az keyvault set-policy --upn $userPrincipalName --name $keyVaultName --secret-permissions set delete get list

    # Create a secret
    password=$(openssl rand -base64 32)
    az keyvault secret set --vault-name $keyVaultName --name 'vmAdminPassword' --value $password
    
    # Print the useful property values
    echo "You need the following values for the virtual machine deployment:"
    echo "Resource group name is: $resourceGroupName."
    echo "The admin password is: $password."
    echo "The Key Vault resource ID is: $keyVaultId."
    ```

    ```azurepowershell-interactive
    $keyVaultName = "<your-unique-vault-name>"
    $resourceGroupName="<your-resource-group-name>"
    $location='Central US'
    $userPrincipalName="<your-email-address-associated-with-your-subscription>"
    
    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
        
    # Create a Key Vault
    $keyVault = New-AzureRmKeyVault `
      -VaultName $keyVaultName `
      -resourceGroupName $resourceGroupName `
      -Location $location `
      -EnabledForTemplateDeployment
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userPrincipalName -PermissionsToSecrets set,delete,get,list
      
    # Create a secret
    $password = openssl rand -base64 32
    
    $secretValue = ConvertTo-SecureString $password -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name "vmAdminPassword" -SecretValue $secretValue
    
    # Print the useful property values
    echo "You need the following values for the virtual machine deployment:"
    echo "Resource group name is: $resourceGroupName."
    echo "The admin password is: $password."
    echo "The Key Vault resource ID is: " $keyVault.ResourceID
    ```
4. 写下输出值。 本教程后面会用到它们

> [!NOTE]
> 每个 Azure 服务具有特定的密码要求。 例如，Azure 虚拟机的要求可以在“创建 VM 时，密码有什么要求？”中找到。

## <a name="open-a-quickstart-template"></a>打开快速入门模板

Azure 快速入门模板是资源管理器模板的存储库。 无需从头开始创建模板，只需找到一个示例模板并对其自定义即可。 本教程中使用的模板称为[部署简单的 Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)。

1. 在 Visual Studio Code 中，选择“文件”>“打开文件”。
2. 在“文件名”中粘贴以下 URL：

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. 选择“打开”以打开该文件。
4. 选择“文件”>“另存为”，将该文件的副本保存到名为 **azuredeploy.json** 的本地计算机。
5. 重复步骤 1-4 以打开 **https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json**，然后将文件另存为 **azuredeploy.parameters.json**。

## <a name="explore-the-template"></a>浏览模板

浏览此部分的模板时，请尝试回答以下问题：

- 在此模板中定义了多少 Azure 资源？
- 其中一个资源是 Azure 存储帐户。  该定义是否与上一教程中使用的定义类似？
- 对于此模板中定义的资源，能否找到模板参考？
- 能否找到资源的依赖项？

1. 在 Visual Studio Code 中折叠元素，直到只能在 **resources** 中看到第一级元素和第二级元素：

    ![Visual Studio Code Azure 资源管理器模板](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    有五个通过此模板定义的资源。
2. 展开第一个资源。 它是一个存储帐户。 此定义应该与上一教程开头使用的定义相同。

    ![Visual Studio Code Azure 资源管理器模板存储帐户定义](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

3. 展开第二个资源。 资源类型为 **Microsoft.Network/publicIPAddresses**。 若要查找模板参考，请浏览到[模板参考](https://docs.microsoft.com/azure/templates/)，在“按标题筛选”字段中输入“单个公共 IP 地址”或“多个公共 IP 地址”。 将资源定义和模板参考进行比较。

    ![Visual Studio Code Azure 资源管理器模板公共 IP 地址定义](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)
4. 重复上一步，找到在此模板中定义的其他资源的模板参考。  将资源定义和参考进行比较。
5. 展开第四个资源：

    ![Visual Studio Code Azure 资源管理器模板 dependsOn](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependson.png)

    使用 dependsOn 元素可将一个资源定义为与一个或多个资源相依赖。 在本示例中，此资源为 networkInterface。  它依赖于两个其他的资源：

    * publicIPAddress
    * virtualNetwork

6. 展开第五个资源。 此资源为虚拟机。 它依赖于两个其他的资源：

    * storageAccount
    * networkInterface

下图演示了此模板的资源和依赖项信息：

![Visual Studio Code Azure 资源管理器模板依赖项图](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

指定依赖项可以让资源管理器有效地部署此解决方案。 它以并行方式部署存储帐户、公共 IP 地址和虚拟网络，因为这些没有依赖项。 部署公共 IP 地址和虚拟网络资源以后，会创建网络接口。 所有其他的资源都部署以后，资源管理器会部署虚拟机。

## <a name="edit-the-parameters-file"></a>编辑参数文件

无需对模板文件进行任何更改。 但是，需要修改参数文件，以便从 Key Vault 检索管理员密码。

1. 在 Visual Studio Code 中打开 **azuredeploy.parameters.json**（如果尚未打开）。
2. 将 **adminPassword** 参数更新为：

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
    将 **id** 替换为在上一过程中创建的 Key Vault 的资源 ID。 它是输出之一。 

    ![集成 Key Vault 和资源管理器模板虚拟机部署参数文件](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. 指定以下值：

    - **adminUsername**：为虚拟机管理员帐户命名。
    - **dnsLabelPrefix**：为 dnsLablePrefix 命名。
4. 保存更改。

## <a name="deploy-the-template"></a>部署模板

可通过多种方法来部署模板。  本教程从 Azure 门户使用 Cloud Shell。

1. 登录到 [Cloud Shell](https://shell.azure.com)。 也可登录到 [Azure 门户](https://portal.azure.com)，然后选择右上角的“Cloud Shell”，如下图所示：

    ![Azure 门户 - Cloud Shell](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell.png)
2. 选择 Cloud Shell 左上角的“PowerShell”，然后选择“确认”。  在本教程中，请使用 PowerShell。
3. 在 Cloud Shell 中选择“上传文件”：

    ![Azure 门户 - Cloud Shell - 上传文件](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
4. 选择前面在本教程中保存的文件。 默认名称为 **azuredeploy.json** 和 **azuredeploy.paraemters.json**。  如果文件的文件名相同，则会覆盖旧文件，没有任何通知。
5. 在 Cloud Shell 中运行以下命令，验证是否已成功上传文件。 

    ```bash
    ls
    ```

    ![Azure 门户 - Cloud Shell - 列出文件](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-list-file.png)

    屏幕截图上显示的文件名为 azuredeploy.json。

6. 在 Cloud Shell 中运行以下命令，验证 JSON 文件的内容。

    ```bash
    cat azuredeploy.json
    cat azuredeploy.parameters.json
    ```
7. 在 Cloud Shell 中运行以下 PowerShell 命令。 示例脚本使用为 Key Vault 创建的资源组。 使用同一资源组可以更容易地清理资源。

    ```powershell
    $resourceGroupName = "<Enter the resource group name>"
    $deploymentName = "<Enter a deployment name>"

    New-AzureRmResourceGroupDeployment -Name $deploymentName `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile azuredeploy.json `
        -TemplateparameterFile azuredeploy.parameters.json
    ```
8. 运行以下 PowerShell 命令，列出新建的虚拟机：

    ```powershell
    Get-AzureRmVM -Name SimpleWinVM -ResourceGroupName $resourceGroupName
    ```

    虚拟机名称在模板中硬编码为 **SimpleWinVM**。

9. 登录到虚拟机，测试管理员的凭据。 

## <a name="clean-up-resources"></a>清理资源

不再需要 Azure 资源时，请通过删除资源组来清理部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”。
2. 在“按名称筛选”字段中输入资源组名称。
3. 选择资源组名称。  应会看到，该资源组中总共有六个资源。
4. 在顶部菜单中选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

本教程介绍如何通过开发和部署模板来创建虚拟机、虚拟网络和依赖资源。 若要了解如何根据条件部署 Azure 资源，请参阅：


> [!div class="nextstepaction"]
> [使用条件](./resource-manager-tutorial-use-conditions.md)

