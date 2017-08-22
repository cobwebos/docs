---
title: "在 Visual Studio 中创建、生成和部署逻辑应用 - Azure 逻辑应用 | Microsoft Docs"
description: "创建 Visual Studio 项目以便设计、生成和部署 Azure 逻辑应用。"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e484e5ce-77e9-4fa9-bcbe-f851b4eb42a6
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 2/14/2017
ms.author: LADocs; jehollan
ms.translationtype: HT
ms.sourcegitcommit: 79bebd10784ec74b4800e19576cbec253acf1be7
ms.openlocfilehash: e7f5cf483d22e4c60dedbe5176ceb0bc8b2b6e66
ms.contentlocale: zh-cn
ms.lasthandoff: 08/03/2017

---

# <a name="design-build-and-deploy-azure-logic-apps-in-visual-studio"></a>在 Visual Studio 中设计、生成和部署 Azure 逻辑应用

虽然 [Azure 门户](https://portal.azure.com/)提供了创建和管理 Azure 逻辑应用的良好方法，但你可以使用 Visual Studio 设计、生成和部署逻辑应用。 Visual Studio 提供了丰富的工具（如逻辑应用设计器），使你能够创建逻辑应用，配置部署和自动化模板，并部署到任何环境。 

若要开始使用 Azure 逻辑应用，请参阅[如何在 Azure 门户中创建第一个逻辑应用](logic-apps-create-a-logic-app.md)。

## <a name="installation-steps"></a>安装步骤

若要安装和配置用于 Azure 逻辑应用的 Visual Studio 工具，请按照以下步骤进行操作。

### <a name="prerequisites"></a>先决条件

* [Visual Studio 2017](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) 或 Visual Studio 2015
* [最新版 Azure SDK](https://azure.microsoft.com/downloads/)（2.9.1 或更高版本）
* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)
* 使用嵌入式设计器访问 Web

### <a name="install-visual-studio-tools-for-azure-logic-apps"></a>安装用于 Azure 逻辑应用的 Visual Studio 工具

安装必备组件后，请执行以下操作：

1. 打开 Visual Studio。 在“工具”菜单上，选择“扩展和更新”。
2. 展开“联机”类别以在线搜索。
3. 浏览或搜索“逻辑应用”，直至找到“适用于 Visual Studio 的 Azure 逻辑应用工具”。
4. 若要下载和安装扩展，请单击“下载”。
5. 安装后重启 Visual Studio。

> [!NOTE]
> 也可以直接从 Visual Studio Marketplace 下载[适用于 Visual Studio 2017 的 Azure 逻辑应用工具](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551)和[适用于 Visual Studio 2015 的 Azure 逻辑应用工具](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio)。

安装完成后，可将 Azure 资源组项目与逻辑应用设计器配合使用。

## <a name="create-your-project"></a>创建项目

1. 在“文件”菜单上，转到“新建”，并选择“项目”。 或者，要将项目添加到现有解决方案，请转到“添加”，然后选择“新建项目”。

    ![“文件”菜单](./media/logic-apps-deploy-from-vs/filemenu.png)

2. 在“新建项目”窗口中，找到“云”，并选择“Azure 资源组”。 为项目命名，并单击“确定”。

    ![添加新项目](./media/logic-apps-deploy-from-vs/addnewproject.png)

3. 选择“逻辑应用”模板，这会创建一个空白的逻辑应用部署模板以供使用。 选择模板后，单击“确定”。

    ![选择逻辑应用模板](./media/logic-apps-deploy-from-vs/selectazuretemplate1.png)

    现在已将逻辑应用项目添加到了解决方案中。 
    部署文件应显示在“解决方案资源管理器”中。

    ![部署文件](./media/logic-apps-deploy-from-vs/deployment.png)

## <a name="create-your-logic-app-with-logic-app-designer"></a>使用逻辑应用设计器创建逻辑应用

如果已具有包含逻辑应用的 Azure 资源组项目，可以在 Visual Studio 中打开逻辑应用设计器以创建工作流。 

> [!NOTE]
> 设计器需要 Internet 连接以查询连接器，以获取可用的属性和数据。 例如，如果使用 Dynamics CRM Online 连接器，设计器将查询 CRM 实例以显示可用的自定义属性和默认属性。

1. 右键单击 `<template>.json` 文件，选择“使用逻辑应用设计器打开”。 (`Ctrl+L`)

2. 选择 Azure 订阅、资源组和部署模板的位置。

    > [!NOTE]
    > 在设计期间，设计逻辑应用会创建查询属性的 API 连接资源。 Visual Studio 使用所选资源组在设计期间创建这些连接。 要查看或更改任何 API 连接，请转到 Azure 门户，并浏览“API 连接”。

    ![订阅选取器](./media/logic-apps-deploy-from-vs/designer_picker.png)

    设计器使用 `<template>.json` 文件中的定义进行绘制。

4. 创建和设计逻辑应用。 将使用更改更新部署模板。

    ![Visual Studio 中的逻辑应用设计器](./media/logic-apps-deploy-from-vs/designer_in_vs.png)

Visual Studio 将 `Microsoft.Web/connections` 资源添加到逻辑应用运行所需的任何连接的资源文件中。 这些连接属性可在部署时进行设置，并在 Azure 门户中的“API 连接”中部署后进行管理。

### <a name="switch-to-json-code-view"></a>切换到 JSON 代码视图

若要显示逻辑应用的 JSON 表示形式，请在设计器底部选择“代码视图”选项卡。

要切换回完整资源 JSON，请右键单击 `<template>.json` 文件，并选择“打开”。

### <a name="add-references-for-dependent-resources-to-visual-studio-deployment-templates"></a>将从属资源的引用添加到 Visual Studio 部署模板

如果希望逻辑应用引用从属资源，可以在逻辑应用部署模板中使用 [Azure 资源管理器模板函数](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions)。 例如，可能想让逻辑应用引用要与逻辑应用一起部署的 Azure 函数或集成帐户。 请遵循以下准则，了解如何在部署模板中使用参数，使逻辑应用设计器可正确地呈现。 

可以在以下类型的触发器和操作中使用逻辑应用参数：

*   子工作流
*   函数应用
*   APIM 调用
*   API 连接运行时 URL
*   API 连接路径

可以使用模板函数，如参数、变量、资源 ID、concat 等。例如，下面是替换 Azure 函数资源 ID 的方法：

```
"parameters":{
    "functionName": {
        "type":"string",
        "minLength":1,
        "defaultValue":"<FunctionName>"
    }
},
```

使用参数的位置：

```
"MyFunction": {
    "type": "Function",
    "inputs": {
        "body":{},
        "function":{
            "id":"[resourceid('Microsoft.Web/sites/functions','functionApp',parameters('functionName'))]"
        }
    },
    "runAfter":{}
}
```
再举一例，可以参数化服务总线发送消息操作：

```
"Send_message": {
    "type": "ApiConnection",
        "inputs": {
            "host": {
                "connection": {
                    "name": "@parameters('$connections')['servicebus']['connectionId']"
                }
            },
            "method": "post",
            "path": "[concat('/@{encodeURIComponent(''', parameters('queueuname'), ''')}/messages')]",
            "body": {
                "ContentData": "@{base64(triggerBody())}"
            },
            "queries": {
                "systemProperties": "None"
            }
        },
        "runAfter": {}
    }
```
> [!NOTE] 
> host.runtimeUrl 是可选的，可从模板中删除（如果存在）。
> 


> [!NOTE] 
> 为了使逻辑应用设计器能工作，在使用参数时必须提供默认值，例如：
> 
> ```
> "parameters": {
>     "IntegrationAccount": {
>     "type":"string",
>     "minLength":1,
>     "defaultValue":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Logic/integrationAccounts/<integrationAccountName>"
>     }
> },
> ```

### <a name="save-your-logic-app"></a>保存逻辑应用

若要随时保存逻辑应用，请转到“文件” > “保存”。 (`Ctrl+S`) 

如果保存应用时逻辑应用出现任何错误，这些错误都会显示在 Visual Studio 的“输出”窗口中。

## <a name="deploy-your-logic-app-from-visual-studio"></a>从 Visual Studio 部署逻辑应用

完成应用配置后，只需几个步骤，即可直接从 Visual Studio 进行部署。 

1. 在“解决方案资源管理器”中，右键单击项目，并转到“部署” > “新部署...”

    ![新部署](./media/logic-apps-deploy-from-vs/newdeployment.png)

2. 出现提示时，请登录到 Azure 订阅。 

3. 现在，必须选择要在其中部署逻辑应用的资源组的详细信息。 完成后，请选择“部署”。

    > [!NOTE]
    > 请确保为资源组选择正确的模板和参数文件。 例如，如果要部署到生产环境，请选择生产参数文件。

    ![部署到资源组](./media/logic-apps-deploy-from-vs/deploytoresourcegroup.png)

    部署状态会显示在“输出”窗口中。 
    需要在“显示输出来源”列表中选择“Azure 预配”。

    ![部署状态输出](./media/logic-apps-deploy-from-vs/output.png)

将来，可以在源控件中编辑逻辑应用，并使用 Visual Studio 部署新版本。

> [!NOTE]
> 如果直接在 Azure 门户中更改定义，那么下次从 Visual Studio 部署时会覆盖这些更改。 

## <a name="add-your-logic-app-to-an-existing-resource-group-project"></a>将逻辑应用添加到现有资源组项目

如果存在现有资源组项目，可在“JSON 大纲”窗口中将逻辑应用添加到该项目。 还可以添加另一个逻辑应用和之前创建的应用。

1. 打开 `<template>.json` 文件。

2. 若要打开“JSON 大纲”窗口，请转到“视图” > “其他窗口” > “JSON 大纲”。

3. 要将资源添加到模板文件，请在“JSON 大纲”窗口顶部单击“添加资源”。 或者在“JSON 大纲”窗口中，右键单击“资源”，并选择“添加新资源”。

    ![“JSON 大纲”窗口](./media/logic-apps-deploy-from-vs/jsonoutline.png)
    
4. 在“添加资源”对话框中，找到并选择“逻辑应用”。 为逻辑应用命名，并选择“添加”。

    ![添加资源](./media/logic-apps-deploy-from-vs/addresource.png)

## <a name="next-steps"></a>后续步骤

* [使用 Visual Studio Cloud Explorer 管理逻辑应用](logic-apps-manage-from-vs.md)
* [查看常见示例和方案](logic-apps-examples-and-scenarios.md)
* [了解如何使用 Azure 逻辑应用自动执行业务流程](http://channel9.msdn.com/Events/Build/2016/T694)
* [了解如何将系统与 Azure 逻辑应用集成](http://channel9.msdn.com/Events/Build/2016/P462)

