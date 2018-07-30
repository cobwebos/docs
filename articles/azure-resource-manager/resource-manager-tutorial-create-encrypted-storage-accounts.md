---
title: 创建用于部署已加密存储帐户的 Azure 资源管理器模板 | Microsoft Docs
description: 使用 Visual Studio Code 创建用于部署已加密存储帐户的模板。
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
ms.openlocfilehash: 7c78636a210ae90c5bfe1d0bfd35e4e05633f5cd
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188193"
---
# <a name="tutorial-create-an-azure-resource-manager-template-for-deploying-an-encrypted-storage-account"></a>教程：创建用于部署已加密存储帐户的 Azure 资源管理器模板

了解如何查找相关信息，以便完成 Azure 资源管理器模板。

在本教程中，请使用 Azure 快速入门模板中提供的基础模板来创建 Azure 存储帐户。  根据模板参考文档自定义基础模板，以便创建加密的存储帐户。

本教程涵盖以下任务：

> [!div class="checklist"]
> * 打开快速入门模板
> * 了解模板格式
> * 使用模板中的参数
> * 使用模板中的变量
> * 编辑模板
> * 部署模板

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要完成本文，需要做好以下准备：

* [Visual Studio Code](https://code.visualstudio.com/)。
* 资源管理器工具扩展。 若要进行安装，请参阅[安装资源管理器扩展](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)。

## <a name="open-a-quickstart-template"></a>打开快速入门模板

本快速入门中使用的模板称为[创建标准存储帐户](https://azure.microsoft.com/resources/templates/101-storage-account-create/)。 该模板定义 Azure 存储帐户资源。

1. 在 Visual Studio Code 中，选择“文件”>“打开文件”。
2. 在“文件名”中粘贴以下 URL：

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. 选择“打开”以打开该文件。
4. 选择“文件”>“另存为”，将该文件作为 **azuredeploy.json** 保存到本地计算机。

## <a name="understand-the-format"></a>了解格式

在 VS Code 中将模板折叠到根级别。 你使用最简单的结构，其中包含以下元素：

![资源管理器模板的最简单结构](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-simplest-structure.png)

* **$schema**：指定描述模板语言版本的 JSON 架构文件所在的位置。
* **contentVersion**：为此元素指定任意值，以便记录模板中的重要更改。
* **parameters**：指定执行部署以自定义资源部署时提供的值。
* **variables**：指定在模板中用作 JSON 片段以简化模板语言表达式的值。
* **resources**：指定已在资源组中部署或更新的资源类型。
* **outputs**：指定部署后返回的值。

## <a name="use-parameters-in-template"></a>使用模板中的参数

可以使用参数提供针对特定环境定制的值，以便自定义部署。 为存储帐户设置值时，请使用模板中定义的参数。

![资源管理器模板参数](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-parameters.png)

在此模板中，定义了两个参数。 注意，一个模板函数用在 location.defaultValue 中：

```json
"defaultValue": "[resourceGroup().location]",
```

resourceGroup() 函数返回表示当前资源组的对象。 有关模板函数的列表，请参阅 [Azure 资源管理器模板函数](./resource-group-template-functions.md)。

若要使用模板中定义的参数，请执行以下代码：

```json
"location": "[parameters('location')]",
"name": "[parameters('storageAccountType')]"
```

## <a name="use-variables-in-template"></a>使用模板中的变量

变量用于构造可在整个模板中使用的值。 变量有助于减轻模板的复杂性。

![资源管理器模板变量](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-variables.png)

此模板定义 *storageAccountName* 变量。 在定义中，使用两个模板函数：

- **concat()**：连接多个字符串。 有关详细信息，请参阅 [concat](./resource-group-template-functions-string.md#concat)。
- **uniqueString()**：根据作为参数提供的值创建确定性哈希字符串。 每个 Azure 存储帐户都必须有在整个 Azure 中都唯一的名称。 此函数提供一个唯一字符串。 如需更多的字符串函数，请参阅[字符串函数](./resource-group-template-functions-string.md)。

若要使用模板中定义的变量，请执行以下代码：

```json
"name": "[variables('storageAccountName')]"
```

## <a name="edit-the-template"></a>编辑模板

若要查找与存储帐户加密相关的配置，可以使用 Azure 存储帐户的模板参考。

1. 浏览到 [Azure 模板](https://docs.microsoft.com/azure/templates/)。
2. 在左侧的 TOC 中，选择“参考”->“存储”->“存储帐户”。 此页包含的信息用于定义存储帐户信息。
3. 了解与加密相关的信息。  
4. 在存储帐户资源定义的 properties 元素中，添加以下 json：

    ```json
    "encryption": {
        "keySource": "Microsoft.Storage",
        "services": {
            "blob": {
                "enabled": true
            }
        }
    }
    ```
    此部分启用 Blob 存储服务的加密功能。

最终的 resources 元素类似于：

![资源管理器模板加密的存储帐户资源](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resources.png)

## <a name="deploy-the-template"></a>部署模板

可通过多种方法来部署模板。  本教程从 Azure 门户使用 Cloud Shell。 Cloud shell 支持 Azure CLI 和 Azure PowerShell。 本文中的说明使用 CLI。

1. 登录到 [Azure 门户](https://portal.azure.com)
2. 如下图所示，选择右上角的“Cloud Shell”：

    ![Azure 门户 - Cloud Shell](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell.png)

3. 选择向下箭头，然后选择“Bash”（如果不是 Bash）。 在本教程中，请使用 Azure CLI。

    ![Azure 门户 - Cloud Shell - CLI](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-choose-cli.png)
4. 选择“重启”以重启 Shell。
5. 依次选择“上传/下载文件”、“上传”。

    ![Azure 门户 - Cloud Shell - 上传文件](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-upload-file.png)
6. 选择前面在本教程中保存的文件。 默认名称为 **azuredeploy.json**。
7. 在 Cloud Shell 中，运行 **ls** 命令来验证是否已成功上传文件。 还可以使用 **cat** 命令来验证模板内容。

    ![Azure 门户 - Cloud Shell - 列出文件](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-list-file.png)
8. 在 Cloud Shell 中运行以下命令：

    ```cli
    az group create --name <ResourceGroupName> --location <AzureLocation>

    az group deployment create --name <DeploymentName> --resource-group <ResourceGroupName> --template-file azuredeploy.json
    ```
    下面是示例部署的屏幕截图：

    ![Azure 门户 - Cloud Shell - 部署模板](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-deploy-template.png)

    屏幕截图中使用了以下值：

    * **&lt;ResourceGroupName>**：myresourcegroup0719。 参数有两种形式。  请确保使用相同的值。
    * **&lt;AzureLocation>**：eastus2
    * **&lt;DeployName>**：mydeployment0719
    * **&lt;TemplateFile>**：azuredeploy.json

    在屏幕截图上的输出中，存储帐户名称为 *fhqbfslikdqdsstandardsa*。 

9. 运行以下 PowerShell 命令列出新建的存储帐户：

    ```cli
    az storage account show --resource-group <ResourceGroupName> --name <StorageAccountName>
    ```

    此时会看到一个类似于以下屏幕截图的输出，指示已为 Blob 存储启用加密。

    ![Azure 资源管理器加密的存储帐户](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-account.png)

## <a name="clean-up-resources"></a>清理资源

不再需要 Azure 资源时，请通过删除资源组来清理部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”。
2. 在“按名称筛选”字段中输入资源组名称。
3. 选择资源组名称。  应会看到，该资源组中总共有六个资源。
4. 在顶部菜单中选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何使用模板参考来自定义现有的模板。 本教程中使用的模板仅包含一个 Azure 资源。  在下一篇教程中，我们将开发包含多个资源的模板。  某些资源具有依赖的资源。

> [!div class="nextstepaction"]
> [创建多个资源](./resource-manager-tutorial-create-templates-with-dependent-resources.md)
