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
ms.date: 09/07/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: d48374d7919be3d141ea199e8238a220dbfe0332
ms.sourcegitcommit: 3150596c9d4a53d3650cc9254c107871ae0aab88
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2018
ms.locfileid: "47419519"
---
# <a name="tutorial-create-an-azure-resource-manager-template-for-deploying-an-encrypted-storage-account"></a>教程：创建用于部署已加密存储帐户的 Azure 资源管理器模板

了解如何查找相关信息，以便完成 Azure 资源管理器模板。

在本教程中，请使用 Azure 快速入门模板中提供的基础模板来创建 Azure 存储帐户。  根据模板参考文档自定义基础模板，以便创建加密的存储帐户。

本教程涵盖以下任务：

> [!div class="checklist"]
> * 打开快速入门模板
> * 了解模板
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

本教程的目标是定义一个模板，以便创建加密的存储帐户。  示例模板仅创建基本的非加密型存储帐户。 若要查找与加密相关的配置，可以使用 Azure 存储帐户的模板参考。

1. 浏览到 [Azure 模板](https://docs.microsoft.com/azure/templates/)。
2. 在“按标题筛选”中，输入“存储帐户”。
3. 选择“参考/模板参考/存储/存储帐户”，如以下屏幕截图中所示：

    ![资源管理器模板参考存储帐户](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts.png)

    资源管理器模板-资源参考-存储帐户
1. 了解与加密相关的信息。  
1. 在存储帐户资源定义的 properties 元素中，添加以下 json：

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

在 Visual Studio Code 中修改模板，使最终的资源元素如下所示：

![资源管理器模板加密的存储帐户资源](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resources.png)

## <a name="deploy-the-template"></a>部署模板

有关部署过程，请参阅 Visual Studio Code 快速入门中的[部署模板](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template)部分。

以下屏幕快照显示的 CLI 命令用于列出新创建的存储帐户，该命令指示已为 Blob 存储启用加密。

![Azure 资源管理器加密的存储帐户](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-account.png)

## <a name="clean-up-resources"></a>清理资源

不再需要 Azure 资源时，请通过删除资源组来清理部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”。
2. 在“按名称筛选”字段中输入资源组名称。
3. 选择资源组名称。  应会看到，该资源组中总共有六个资源。
4. 在顶部菜单中选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何使用模板参考来自定义现有的模板。 若要了解如何创建多个存储帐户实例，请参阅：

> [!div class="nextstepaction"]
> [创建多个实例](./resource-manager-tutorial-create-multiple-instances.md)
