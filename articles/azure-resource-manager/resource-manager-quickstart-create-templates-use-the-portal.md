---
title: 使用 Azure 门户创建和部署 Azure 资源管理器模板 | Microsoft Docs
description: 了解如何使用 Azure 门户创建第一个 Azure 资源管理器模板，以及如何部署该模板。
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/17/2018
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: 62bc29495bab3446b6131223110c694e53412db9
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2018
ms.locfileid: "49407633"
---
# <a name="quickstart-create-and-deploy-azure-resource-manager-templates-by-using-the-azure-portal"></a>快速入门：使用 Azure 门户创建和部署 Azure 资源管理器模板

了解如何使用 Azure 门户生成第一个 Azure 资源管理器模板，以及从 Azure 门户编辑和部署该模板的过程。 Resource Manager 模板为 JSON 文件，用于定义针对解决方案进行部署时所需的资源。 本教程中的说明将创建一个 Azure 存储帐户。 可以使用相同的过程来创建其他 Azure 资源。

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="generate-a-template-using-the-portal"></a>使用门户生成模板

在本部分，我们将使用 Azure 门户创建一个存储帐户。 在部署存储帐户之前，可以使用相应的选项浏览门户根据配置生成的模板。 可以保存模板，便于将来重复使用。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“创建资源” > “存储” > “存储帐户 - Blob、文件、表、队列”。

    ![使用 Azure 门户创建 Azure 存储帐户](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-portal.png)
3. 输入以下信息。 

    - **资源组**：使用所选的名称创建新的 Azure 资源组。 在屏幕截图中，资源组名称为 *mystorage1016rg*。
    - **名称**：为存储帐户指定唯一的名称。 在屏幕截图中，名称为 *mystorage1016*。

    可对剩余的属性使用默认值。

    ![使用 Azure 门户创建 Azure 存储帐户配置](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account.png)

    > [!NOTE]
    > 某些导出的模板需要经过一些编辑才能部署。

4. 在屏幕底部选择“查看 + 创建”。 
5. 在屏幕底部选择“下载自动化模板”。 门户显示生成的模板：

    ![通过门户生成模板](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template.png)

    主窗格会显示该模板。 它是包含四个顶级元素（`schema`、`contentVersion`、`parameters`、`resources`）的 JSON 文件。 有关详细信息，请参阅[了解 Azure 资源管理器模板的结构和语法](./resource-group-authoring-templates.md)

    定义了六个参数。 其中一个名为 **storageAccountName**。 第二个突出显示的部分显示如何在模板中使用此参数。 在下一部分，请编辑此模板，以便将生成的名称用于存储帐户。

    在模板中定义了一个 Azure 资源。 类型为 [Microsoft.Storage/storageAccounts]。 查看资源定义方式和定义结构。
6. 选择“下载”。 将已下载包中的 **template.json** 保存到计算机。 在下一部分，请使用模板部署工具来编辑此模板。
7. 选择“参数”选项卡，查看为参数提供的值。 部署模板时，请写下这些值，下一部分需要它们。

    ![通过门户生成模板](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template-parameters.png)

    使用模板和 parameters 文件可以创建一个 Azure 存储帐户。

## <a name="edit-and-deploy-the-template"></a>编辑和部署模板

可以使用 Azure 门户执行一些基本的模板编辑操作。 在本快速入门中，请使用名为“模板部署”的门户工具。 若要编辑更复杂的模板，请考虑使用 [Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md)，因为它提供更丰富的编辑功能。

Azure 要求每个 Azure 服务都具有唯一的名称。 如果输入已存在的存储帐户名，则部署将失败。 若要避免此问题，可以使用模板函数调用 `uniquestring()`，以生成唯一的存储帐户名。

1. 在 Azure 门户中，选择“创建资源”。
2. 在“搜索市场”中键入“模板部署”，然后按 **ENTER**。
3. 选择“模板部署”。

    ![Azure 资源管理器模板库](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-library.png)
4. 选择“创建”。
5. 选择“在编辑器中生成自己的模板”。
6. 选择“加载文件”，然后按说明加载在上部分下载的 template.json。
7. 添加一个变量，如以下屏幕截图所示：

    ```json
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
    ```
    ![Azure 资源管理器模板](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-edit-storage-account-template-revised.png)

    此处使用了两个函数：`concat()` 和 `uniqueString()`。

8. 删除上面屏幕截图中突出显示的 **storageAccountName** 参数。
9. 更新 **Microsoft.Storage/storageAccounts** 资源的 name 元素，以使用新定义的变量而不是参数：

    ```json
    "name": "[variables('storageAccountName')]",
    ```

    最终的模板应如下所示：

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "location": {
                "type": "string"
            },
            "accountType": {
                "type": "string"
            },
            "kind": {
                "type": "string"
            },
            "accessTier": {
                "type": "string"
            },
            "supportsHttpsTrafficOnly": {
                "type": "bool"
            }
        },
        "variables": {
            "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
        },
        "resources": [
            {
                "name": "[variables('storageAccountName')]",
                "type": "Microsoft.Storage/storageAccounts",
                "apiVersion": "2018-07-01",
                "location": "[parameters('location')]",
                "properties": {
                    "accessTier": "[parameters('accessTier')]",
                    "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]"
                },
                "dependsOn": [],
                "sku": {
                    "name": "[parameters('accountType')]"
                },
                "kind": "[parameters('kind')]"
            }
        ],
        "outputs": {}
    }
    ```
7. 选择“保存”。
8. 输入以下值：

    - **资源组**：使用唯一的名称为资源组命名。
    - **位置**：选择资源组的位置。
    - **位置**：选择存储帐户的位置。  可以使用与资源组相同的位置。
    - **帐户类型**：对于本快速入门，请输入 **Standard_LRS**。
    - **种类**：对于本快速入门，请输入“StorageV2”。
    - **访问层**：在本快速入门中，请输入**热**。
    - **启用仅限 Https 流量**。  对于本快速入门，请选择“true”。
    - **我同意上述条款和条件**：（选中）

    下面是示例部署的屏幕截图：

    ![Azure 资源管理器模板部署](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-deploy.png)

10. 选择“购买”。
11. 选择屏幕顶部的铃铛图标（通知）可查看部署状态。 等待部署完成。

    ![Azure 资源管理器模板部署通知](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-notification.png)

12. 从通知窗格选择“转到资源组”。 将看到类似于以下的屏幕：

    ![Azure 资源管理器模板部署资源组](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-deployment-resource-group.png)

    可以看到部署状态为成功，资源组中只有一个存储帐户。 存储帐户名称是模板生成的唯一字符串。 若要了解有关使用 Azure 存储帐户的更多信息，请参阅[快速入门：使用 Azure 门户上载、下载和列出 blob](../storage/blobs/storage-quickstart-blobs-portal.md)。

## <a name="clean-up-resources"></a>清理资源

不再需要 Azure 资源时，请通过删除资源组来清理部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”。
2. 在“按名称筛选”字段中输入资源组名称。
3. 选择资源组名称。  应会看到资源组中的存储帐户。
4. 在顶部菜单中选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

本教程已介绍如何通过 Azure 门户生成模板，以及如何使用门户部署模板。 本快速入门中使用的模板是包含一个 Azure 资源的简单模板。 如果模板较为复杂，使用 Visual Studio Code 或 Visual Studio 开发模板会更方便。 下一快速入门还介绍如何使用 Azure PowerShell 和 Azure 命令行界面 (CLI) 来部署模板。

> [!div class="nextstepaction"]
> [使用 Visual Studio Code 创建模板](./resource-manager-quickstart-create-templates-use-visual-studio-code.md)
