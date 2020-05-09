---
title: 具有依赖资源的模板
description: 了解如何使用多个资源创建 Azure 资源管理器模板，以及如何使用 Azure 门户部署该模板
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: cf876d3c7c100f001ba81082d792e81a777c7315
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82193031"
---
# <a name="tutorial-create-arm-templates-with-dependent-resources"></a>教程：创建包含所依赖资源的 ARM 模板

了解如何创建 Azure 资源管理器 (ARM) 模板以部署多个资源并配置部署顺序。 创建模板后，从 Azure 门户使用 Cloud Shell 部署该模板。

本教程介绍如何创建存储帐户、虚拟机、虚拟网络以及一些其他的依赖资源。 某些资源的部署依赖于另一资源的存在。 例如，创建虚拟机的前提是其存储帐户和网络接口存在。 可通过将一个资源标记为依赖于其他资源来定义此关系。 Resource Manager 将评估资源之间的依赖关系，并根据其依赖顺序进行部署。 如果资源互不依赖，资源管理器将以并行方式部署资源。 有关详细信息，请参阅[在 ARM 模板中定义部署资源的顺序](./define-resource-dependency.md)。

![资源管理器模板依赖资源部署顺序图](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-dependent-resources-diagram.png)

本教程涵盖以下任务：

> [!div class="checklist"]
> * 打开快速入门模板
> * 浏览模板
> * 部署模板

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要完成本文，需要做好以下准备：

* 包含资源管理器工具扩展的 Visual Studio Code。 请参阅[使用 Visual Studio Code 创建 ARM 模板](use-vs-code-to-create-template.md)。
* 若要提高安全性，请使用为虚拟机管理员帐户生成的密码。 以下是密码生成示例：

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault 旨在保护加密密钥和其他机密。 有关详细信息，请参阅[教程：在 ARM 模板部署中集成 Azure Key Vault](./template-tutorial-use-key-vault.md)。 我们还建议你每三个月更新一次密码。

## <a name="open-a-quickstart-template"></a>打开快速入门模板

Azure 快速入门模板是 ARM 模板的存储库。 无需从头开始创建模板，只需找到一个示例模板并对其自定义即可。 本教程中使用的模板称为[部署简单的 Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)。

1. 在 Visual Studio Code 中，选择“文件”>“打开文件”。  
2. 在“文件名”中粘贴以下 URL： 

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. 选择“打开”以打开该文件。 
4. 选择“文件”>“另存为”，将该文件的副本保存到名为 **azuredeploy.json** 的本地计算机。  

## <a name="explore-the-template"></a>浏览模板

浏览此部分的模板时，请尝试回答以下问题：

* 在此模板中定义了多少 Azure 资源？
* 其中一个资源是 Azure 存储帐户。  该定义是否与上一教程中使用的定义类似？
* 对于此模板中定义的资源，能否找到模板参考？
* 能否找到资源的依赖项？

1. 在 Visual Studio Code 中折叠元素，直到只能在 **resources** 中看到第一级元素和第二级元素：

    ![Visual Studio Code Azure 资源管理器模板](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    有六个通过此模板定义的资源：

   * [Microsoft.Storage/storageAccounts](/azure/templates/Microsoft.Storage/storageAccounts)  。
   * [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)  。
   * [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)  。
   * [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)  。
   * [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)  。
   * [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)  。

     在自定义模板之前查看模板参考会很有帮助。

1. 展开第一个资源。 它是一个存储帐户。 将资源定义和[模板参考](/azure/templates/Microsoft.Storage/storageAccounts)进行比较。

    ![Visual Studio Code Azure 资源管理器模板存储帐户定义](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

1. 展开第二个资源。 资源类型为 `Microsoft.Network/publicIPAddresses`。 将资源定义和[模板参考](/azure/templates/microsoft.network/publicipaddresses)进行比较。

    ![Visual Studio Code Azure 资源管理器模板公共 IP 地址定义](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)

1. 展开第三个资源。 资源类型为 `Microsoft.Network/networkSecurityGroups`。 将资源定义和[模板参考](/azure/templates/microsoft.network/networksecuritygroups)进行比较。

    ![Visual Studio Code Azure 资源管理器模板网络安全组定义](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-network-security-group-definition.png)

1. 展开第四个资源。 资源类型为 `Microsoft.Network/virtualNetworks`：

    ![Visual Studio Code Azure 资源管理器模板虚拟网络 dependsOn](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-virtual-network-definition.png)

    使用 dependsOn 元素可将一个资源定义为与一个或多个资源相依赖。 此资源依赖于另一个资源：

    * `Microsoft.Network/networkSecurityGroups`

1. 展开第五个资源。 资源类型为 `Microsoft.Network/networkInterfaces`。 此资源依赖于两个其他的资源：

    * `Microsoft.Network/publicIPAddresses`
    * `Microsoft.Network/virtualNetworks`

1. 展开第六个资源。 此资源为虚拟机。 它依赖于两个其他的资源：

    * `Microsoft.Storage/storageAccounts`
    * `Microsoft.Network/networkInterfaces`

下图演示了此模板的资源和依赖项信息：

![Visual Studio Code Azure 资源管理器模板依赖项图](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

指定依赖项可以让资源管理器有效地部署此解决方案。 它以并行方式部署存储帐户、公共 IP 地址和虚拟网络，因为这些没有依赖项。 部署公共 IP 地址和虚拟网络资源以后，会创建网络接口。 所有其他的资源都部署以后，资源管理器会部署虚拟机。

## <a name="deploy-the-template"></a>部署模板

1. 登录到 [Azure Cloud Shell](https://shell.azure.com)

1. 通过在左上角选择“PowerShell”  或“Bash”  （适用于 CLI）来选择你喜欢使用的环境。  进行切换时，需重启 shell。

    ![Azure 门户 - Cloud Shell - 上传文件](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. 依次选择“上传/下载文件”、“上传”。   请参阅上面的屏幕截图。 选择先前保存的文件。 上传文件后，可以使用 ls  命令和 cat  命令验证文件是否已成功上传。

1. 运行以下 PowerShell 脚本以部署该模板。

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    echo "Enter the virtual machine admin username:" &&
    read adminUsername &&
    echo "Enter the DNS label prefix:" &&
    read dnsLabelPrefix &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location $location &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json" --parameters adminUsername=$adminUsername dnsLabelPrefix=$dnsLabelPrefix
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
    $adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS label prefix"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $adminUsername `
        -adminPassword $adminPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -TemplateFile "$HOME/azuredeploy.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    ---

1. 通过 RDP 连接到虚拟机，验证虚拟机是否已成功创建。

## <a name="clean-up-resources"></a>清理资源

不再需要 Azure 资源时，请通过删除资源组来清理部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”  。
2. 在“按名称筛选”字段中输入资源组名称。 
3. 选择资源组名称。 应该会看到，该资源组中总共有六个资源。
4. 在顶部菜单中选择“删除资源组”。 

## <a name="next-steps"></a>后续步骤

本教程介绍如何通过开发和部署模板来创建虚拟机、虚拟网络和依赖资源。 若要了解如何使用部署脚本来执行部署前/后操作，请参阅：

> [!div class="nextstepaction"]
> [使用部署脚本](./template-tutorial-deployment-script.md)
