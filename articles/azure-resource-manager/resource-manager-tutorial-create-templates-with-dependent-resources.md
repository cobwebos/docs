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
ms.date: 07/20/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: bd559cb9f0140706a4b9735c642367e03616a14d
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188159"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>教程：使用依赖的资源创建 Azure 资源管理器模板

了解如何创建 Azure 资源管理器模板，以便部署多个资源。  创建模板以后，请通过 Azure 门户使用 Cloud Shell 部署该模板。

某些资源的部署依赖于另一资源的存在。 例如，创建虚拟机的前提是其存储帐户和网络接口存在。 可通过将一个资源标记为依赖于其他资源来定义此关系。 Resource Manager 将评估资源之间的依赖关系，并根据其依赖顺序进行部署。 如果资源互不依赖，资源管理器将以并行方式部署资源。 有关详细信息，请参阅[定义 Azure 资源管理器模板中部署资源的顺序](./resource-group-define-dependencies.md)。

> [!div class="checklist"]
> * 打开快速入门模板
> * 浏览模板
> * 部署模板
> * 清理资源

本教程介绍如何创建虚拟机、虚拟网络以及一些其他的依赖资源。 

## <a name="prerequisites"></a>先决条件

若要完成本文，需要做好以下准备：

* [Visual Studio Code](https://code.visualstudio.com/)。
* 资源管理器工具扩展。  请参阅[安装扩展](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)

## <a name="open-a-quickstart-template"></a>打开快速入门模板

Azure 快速入门模板是资源管理器模板的存储库。 无需从头开始创建模板，只需找到一个示例模板并对其自定义即可。 本教程中使用的模板称为[部署简单的 Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)。

1. 在 Visual Studio Code 中，选择“文件”>“打开文件”。
2. 在“文件名”中粘贴以下 URL：

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. 选择“打开”以打开该文件。
4. 选择“文件”>“另存为”，将该文件的副本保存到名为 **azuredeploy.json** 的本地计算机。

## <a name="explore-the-template"></a>浏览模板

1. 在 Visual Studio Code 中折叠元素，直到只能在 **resources** 中看到第一级元素和第二级元素：

    ![Visual Studio Code Azure 资源管理器模板](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    有五个通过此模板定义的资源。
2. 展开第四个元素：

    ![Visual Studio Code Azure 资源管理器模板 dependsOn](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependson.png)

    使用 dependsOn 元素可将一个资源定义为与一个或多个资源相依赖。 在本示例中，此资源为 networkInterface。  它依赖于两个其他的资源：

    * publicIPAddress
    * virtualNetwork

3. 展开第五个元素。 此资源为虚拟机。 它依赖于两个其他的资源：

    * storageAccount
    * networkInterface

下图演示了此模板的资源和依赖项信息：

![Visual Studio Code Azure 资源管理器模板依赖项图](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

指定依赖项可以让资源管理器有效地部署此解决方案。 它以并行方式部署存储帐户、公共 IP 地址和虚拟网络，因为这些没有依赖项。 部署公共 IP 地址和虚拟网络资源以后，会创建网络接口。 所有其他的资源都部署以后，资源管理器会部署虚拟机。

## <a name="deploy-the-template"></a>部署模板

可通过多种方法来部署模板。  本教程从 Azure 门户使用 Cloud Shell。

1. 登录到 [Azure 门户](https://portal.azure.com)
2. 如下图所示，选择右上角的“Cloud Shell”：

    ![Azure 门户 - Cloud Shell](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell.png)
3. 选择 Cloud Shell 左上角的“PowerShell”。  在本教程中，请使用 PowerShell。
4. 选择“重启”
5. 在 Cloud Shell 中选择“上传文件”：

    ![Azure 门户 - Cloud Shell - 上传文件](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
6. 选择前面在本教程中保存的文件。 默认名称为 **azuredeploy.json**。  如果某个文件的文件名相同，则会覆盖旧文件，没有任何通知。
7. 在 Cloud Shell 中运行以下命令，验证是否已成功上传文件。 

    ```shell
    ls
    ```

    ![Azure 门户 - Cloud Shell - 列出文件](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-list-file.png)

    屏幕截图上显示的文件名为 azuredeploy.json。

8. 在 Cloud Shell 中运行以下命令，验证 JSON 文件的内容。

    ```shell
    cat azuredeploy.json
    ```
9. 在 Cloud Shell 中运行以下 PowerShell 命令：

    ```powershell
    $resourceGroupName = "<Enter the resource group name>"
    $location = "<Enter the Azure location>"
    $vmAdmin = "<Enter the admin username>"
    $vmPassword = "<Enter the password>"
    $dnsLabelPrefix = "<Enter the prefix>"

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    $vmPW = ConvertTo-SecureString -String $vmPassword -AsPlainText -Force
    New-AzureRmResourceGroupDeployment -Name mydeployment0710 -ResourceGroupName $resourceGroupName `
        -TemplateFile azuredeploy.json -adminUsername $vmAdmin -adminPassword $vmPW `
        -dnsLabelPrefix $dnsLabelPrefix
    ```
    下面是示例部署的屏幕截图：

    ![Azure 门户 - Cloud Shell - 部署模板](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-deploy-template.png)

    屏幕截图中使用了以下值：

    * **$resourceGroupName**：myresourcegroup0710。 
    * **$location**：eastus2
    * **&lt;DeployName>**：mydeployment0710
    * **&lt;TemplateFile>**：azuredeploy.json
    * **模板参数**：

        * **adminUsername**：JohnDole
        * **adminPassword**：Pass@word123
        * **dnsLabelPrefix**：myvm0710

10. 运行以下 PowerShell 命令，列出新建的虚拟机：

    ```powershell
    Get-AzureRmVM -Name SimpleWinVM -ResourceGroupName <ResourceGroupName>
    ```

    虚拟机名称在模板中硬编码为 **SimpleWinVM**。

## <a name="clean-up-resources"></a>清理资源

不再需要 Azure 资源时，请通过删除资源组来清理部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”。
2. 在“按名称筛选”字段中输入资源组名称。
3. 选择资源组名称。  应会看到，该资源组中总共有六个资源。
4. 在顶部菜单中选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

本教程介绍如何通过开发和部署模板来创建虚拟机、虚拟网络和依赖资源。 若要详细了解模板，请参阅[了解 Azure 资源管理器模板的结构和语法](./resource-group-authoring-templates.md)。