---
title: 创建链接的 Azure 资源管理器模板 | Microsoft Docs
description: 了解如何创建链接的 Azure 资源管理器模板，以便创建虚拟机
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
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: d25ca14b78465a6c4fec7e90bc20436e3ca553fc
ms.sourcegitcommit: 3150596c9d4a53d3650cc9254c107871ae0aab88
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2018
ms.locfileid: "47419621"
---
# <a name="tutorial-create-linked-azure-resource-manager-templates"></a>教程：创建链接的 Azure 资源管理器模板

了解如何创建链接的 Azure 资源管理器模板。 使用链接的模板，可以通过一个模板调用另一个模板。 这适用于模块化模板。 在本教程中使用的模板与在[教程：使用资源管理器模板创建多个资源实例](./resource-manager-tutorial-create-multiple-instances.md)中使用的模板相同，该模板用于创建虚拟机、虚拟网络以及其他依赖资源（包括存储帐户）。 请将存储帐户资源分隔到链接的模板。

本教程涵盖以下任务：

> [!div class="checklist"]
> * 打开快速入门模板
> * 创建链接的模板
> * 上传链接的模板
> * 链接到链接的模板
> * 配置依赖项
> * 从链接模板中获取值
> * 部署模板

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要完成本文，需要做好以下准备：

* [Visual Studio Code](https://code.visualstudio.com/)。
* 资源管理器工具扩展。  请参阅[安装扩展](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)。
* 完成[教程：使用资源管理器模板创建多个资源实例](./resource-manager-tutorial-create-multiple-instances.md)。

## <a name="open-a-quickstart-template"></a>打开快速入门模板

Azure 快速入门模板是资源管理器模板的存储库。 无需从头开始创建模板，只需找到一个示例模板并对其自定义即可。 本教程中使用的模板称为[部署简单的 Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)。 这是在[教程：使用资源管理器模板创建多个资源实例](./resource-manager-tutorial-create-multiple-instances.md)中使用的模板。 请将要使用的同一模板的两个副本另存为：

- **主模板**：创建除存储帐户之外的所有资源。
- **链接的模板**：创建存储帐户。

1. 在 Visual Studio Code 中，选择“文件”>“打开文件”。
2. 在“文件名”中粘贴以下 URL：

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. 选择“打开”以打开该文件。
4. 选择“文件”>“另存为”，将该文件的副本保存到名为 **azuredeploy.json** 的本地计算机。
5. 选择“文件”>“另存为”，使用名称 **linkedTemplate.json** 创建文件的另一副本。

## <a name="create-the-linked-template"></a>创建链接的模板

链接的模板可创建存储帐户。 链接的模板与创建存储帐户的独立模板几乎完全相同。 在本教程中，链接的模板需将一个值传回主模板。 此值在 `outputs` 元素中定义。

1. 在 Visual Studio Code 中打开 linkedTemplate.json（如果尚未打开）。
2. 进行以下更改：

    - 删除除存储帐户之外的所有资源。 总共删除四个资源。
    - 更新 **outputs** 元素，使之看起来类似于：

        ```json
        "outputs": {
            "storageUri": {
                "type": "string",
                "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
              }
        }
        ```
        **storageUri** 是主模板中的虚拟机资源定义所需的。  请将该值作为输出值传回主模板。
    - 删除从未用过的参数。 这些参数的下面有绿色的波浪线。 应该只留下一个名为 **location** 的参数。
    - 删除 **variables** 元素。 它们在本教程中不需要。
    - 添加名为 **storageAccountName** 的参数。 存储帐户名称作为参数从主模板传给链接的模板。

    完成后，模板应如下所示：

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "storageAccountName":{
            "type": "string",
            "metadata": {
              "description": "Azure Storage account name."
            }
          },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
              "description": "Location for all resources."
            }
          }
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccountName')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "sku": {
              "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
          }
        ],
        "outputs": {
            "storageUri": {
                "type": "string",
                "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
              }
        }
    }
    ```
3. 保存更改。

## <a name="upload-the-linked-template"></a>上传链接的模板

模板必须能够从运行部署时所在的位置进行访问。 该位置可以是 Azure 存储帐户、Github 或 Dropbox。 如果模板包含敏感信息，请确保限制对其的访问。 在本教程中使用的 Cloud Shell 部署方法就是在[教程：使用资源管理器模板创建多个资源实例](./resource-manager-tutorial-create-multiple-instances.md)中使用的。 主模板 (azuredeploy.json) 会上传到 Shell。 链接的模板 (linkedTemplate.json) 必须在某个位置共享。  为了减少本教程的任务，在上一部分定义的链接的模板已上传到[某个 Azure 存储帐户](https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json)。

## <a name="call-the-linked-template"></a>调用链接的模板

主模板名为 azuredeploy.json。

1. 在 Visual Studio Code 中打开 azuredeploy.json（如果尚未打开）。
2. 从模板中删除存储帐户资源定义。
3. 将以下 json 代码片段添加到保存存储帐户定义的位置。

    ```json
    {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri":"https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json"
          },
          "parameters": {
              "storageAccountName":{"value": "[variables('storageAccountName')]"},
              "location":{"value": "[parameters('location')]"}
          }
      }
    },
    ```

    请注意以下细节：

    - 主模板中的 `Microsoft.Resources/deployments` 资源用于链接到另一模板。
    - `deployments` 资源的名称为 `linkedTemplate`。 此名称用于[配置依赖项](#configure-dependency)。  
    - 调用链接的模板时，只能使用[增量](./deployment-modes.md)部署模式。
    - `templateLink/uri` 包含链接的模板的 URI。 链接的模板已上传到某个共享的存储帐户。 如果将模板上传到 Internet 中的其他位置，则可更新此 URI。
    - 请使用 `parameters` 将值从主模板传递给链接的模板。
4. 保存更改。

## <a name="configure-dependency"></a>配置依赖项

回想一下，在[教程：使用资源管理器模板创建多个资源实例](./resource-manager-tutorial-create-multiple-instances.md)中，虚拟机资源依赖于存储帐户：

![Azure 资源管理器模板依赖项图](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-visual-studio-code-dependency-diagram.png)

由于存储帐户现在是在链接的模板中定义的，因此必须更新 `Microsoft.Compute/virtualMachines` 资源的下述两个元素。

- 重新配置 `dependOn` 元素。 存储帐户定义移到链接的模板。
- 重新配置 `properties/diagnosticsProfile/bootDiagnostics/storageUri` 元素。 在[创建链接的模板](#create-the-linked-template)中，已添加输出值：

    ```json
    "outputs": {
        "storageUri": {
            "type": "string",
            "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
    }
    ```
    此值是主模板所需的。

1. 在 Visual Studio Code 中打开 azuredeploy.json（如果尚未打开）。
2. 展开虚拟机资源定义，更新 **dependsOn**，如以下屏幕截图所示：

    ![Azure 资源管理器链接模板用于配置依赖项 ](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-linked-templates-configure-dependency.png)
    
    “linkedTemplate”是部署资源的名称。  
3. 更新 **properties/diagnosticsProfile/bootDiagnostics/storageUri**，如上一屏幕截图所示。

有关详细信息，请参阅[部署 Azure 资源时使用链接的和嵌套的模板](./resource-group-linked-templates.md)

## <a name="deploy-the-template"></a>部署模板

有关部署过程，请参阅[部署模板](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template)部分。

## <a name="clean-up-resources"></a>清理资源

不再需要 Azure 资源时，请通过删除资源组来清理部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”。
2. 在“按名称筛选”字段中输入资源组名称。
3. 选择资源组名称。  应会看到，该资源组中总共有六个资源。
4. 在顶部菜单中选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

本教程开发和部署链接的模板。 若要了解如何跨多个区域部署 Azure 资源，以及如何使用安全部署做法，请参阅


> [!div class="nextstepaction"]
> [使用 Azure 部署管理器](./deployment-manager-tutorial.md)

