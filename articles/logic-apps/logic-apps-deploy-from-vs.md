---
title: "在 Visual Studio 中生成 Azure 逻辑应用 | Microsoft Docs"
description: "在 Visual Studio 中创建一个项目用于创建并部署逻辑应用。"
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
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: a9f786259676a1bc9aa616f2db2935dff45475e2
ms.openlocfilehash: db818b2b76d3a8d8c3324c9556237139d319efb9


---
# <a name="build-and-deploy-logic-apps-in-visual-studio"></a>在 Visual Studio 中生成和部署逻辑应用
虽然 [Azure 门户](https://portal.azure.com/)可以很好地设计和管理逻辑应用，但还是建议你在 Visual Studio 中设计和部署逻辑应用。  逻辑应用具有丰富的 Visual Studio 工具集，通过该工具集可使用设计器生成逻辑应用、配置任何部署和自动化模板，并将其部署到任何环境。  

## <a name="installation-steps"></a>安装步骤
下面是安装和配置用于逻辑应用的 Visual Studio 工具的步骤。

### <a name="prerequisites"></a>先决条件
* [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [最新版 Azure SDK](https://azure.microsoft.com/downloads/)（2.9.1 或更高版本）
* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)
* 使用嵌入式设计器访问 Web

### <a name="install-visual-studio-tools-for-logic-apps"></a>安装用于逻辑应用的 Visual Studio 工具
安装了必备软件后， 

1. 打开 Visual Studio 2015，转至“工具”菜单，选择“扩展和更新”
2. 选择“联机”类别以在线搜索
3. 搜索“逻辑应用”，显示“适用于 Visual Studio 的 Azure 逻辑应用工具”
4. 单击“下载”按钮，下载并安装此扩展
5. 安装后重启 Visual Studio

> [!NOTE]
> 还可以直接从[此链接](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9)下载扩展
> 
> 

安装完成后，可将 Azure 资源组项目与逻辑应用设计器配合使用。

## <a name="create-a-project"></a>创建一个项目
1. 转到“文件”菜单，选择“新建” >  “项目”（或者，转到“添加”，然后选择“新建项目”将其添加到现有解决方案）： ![文件菜单](./media/logic-apps-deploy-from-vs/filemenu.png)
2. 在对话框中查找“云”，然后选择“Azure 资源组”。 键入“名称”，单击“确定”。
    ![添加新项目](./media/logic-apps-deploy-from-vs/addnewproject.png)
3. 选择**逻辑应用**模板。 此步骤创建空白的逻辑应用部署模板，之后可继续编辑。
    ![选择 Azure 模板](./media/logic-apps-deploy-from-vs/selectazuretemplate1.png)
4. 选择**模板**后，单击“确定”。
   
    现在，逻辑应用项目已添加到解决方案中。 可以在解决方案资源管理器中看见部署文件：  
   
    ![部署](./media/logic-apps-deploy-from-vs/deployment.png)

## <a name="using-the-logic-app-designer"></a>使用逻辑应用设计器
在具有包含逻辑应用的 Azure 资源组项目后，可以在 Visual Studio 中打开设计器以帮助创建工作流。  设计器需连接 Internet 来查询连接器，以获取可用属性和数据（例如，如果使用 Dynamics CRM Online 连接器，设计器将查询 CRM 实例以列出可用自定义属性和默认属性）。

1. 右键单击 `<template>.json` 文件，选择“使用逻辑应用设计器打开”（或 `Ctrl+L`）
2. 选择订阅、资源组和部署模板的位置
   * 值得注意的是，设计逻辑应用可创建 **API 连接**资源，以便在设计期间查询属性。  所选资源组在设计期间用于创建这些连接。  可转到 Azure 门户并浏览“API 连接”来查看或修改任何 API 连接。
   
     ![订阅选取器](./media/logic-apps-deploy-from-vs/designer_picker.png)
3. 根据 `<template>.json` 文件中的定义呈现设计器。
4. 现在可以创建和设计逻辑应用，并在部署模板中更新更改。
    ![Visual Studio 中的设计器](./media/logic-apps-deploy-from-vs/designer_in_vs.png)

`Microsoft.Web/connections` 资源会添加到逻辑应用运行所需的任何连接的资源文件。  这些连接属性可在部署时进行设置，并在 Azure 门户中的“API 连接”中部署后进行管理。

### <a name="switching-to-the-json-code-view"></a>切换到 JSON 代码视图
可以选择设计器底部的“代码视图”选项卡，以切换到逻辑应用的 JSON 表示形式。  若要切换回完整资源 JSON，请右键单击 `<template>.json` 文件，然后选择“打开”。

### <a name="saving-the-logic-app"></a>保存逻辑应用
可以随时通过“保存”按钮或 `Ctrl+S` 来保存逻辑应用。  如果在保存时逻辑应用出现任何错误，这些错误将显示在 Visual Studio 的“输出”窗口中。

## <a name="deploying-your-logic-app"></a>部署逻辑应用
最后，配置完应用后，只需几个步骤，就可以直接从 Visual Studio 中进行部署。 

1. 在解决方案资源管理器中右键单击项目，并转到“部署”**** > “新建部署...”****
    ![新建部署](./media/logic-apps-deploy-from-vs/newdeployment.png)
2. 系统提示你登录 Azure 订阅。 
3. 现在，需要选择要将逻辑应用部署到的资源组的详细信息。 
    ![部署到资源组](./media/logic-apps-deploy-from-vs/deploytoresourcegroup.png)
   
   > [!NOTE]
   > 请务必选择资源组的正确模板和参数文件（例如，如果要部署到生产环境中，则需要选择生产参数文件）。 
   > 
   > 
4. 选择“部署”按钮
5. 部署的状态显示在“输出”窗口中（可能需要选择“Azure 预配”）。 
    ![输出](./media/logic-apps-deploy-from-vs/output.png)

将来，可以在源控件中修改逻辑应用，并使用 Visual Studio 部署新版本。 

> [!NOTE]
> 如果直接在 Azure 门户中修改定义，那么下次从 Visual Studio 部署时将覆盖这些更改。
> 
> 

## <a name="adding-a-logic-app-to-an-existing-resource-group-project"></a>将逻辑应用添加到现有资源组项目
如果有现有资源组项目，则将逻辑应用添加到其中，或者将另一逻辑应用添加到之前创建的应用旁，此操作可通过“JSON 大纲”窗口完成。
1. 打开 `<template>.json` 文件。
2. 打开“JSON 大纲”窗口。  依次通过“视图” > “其他窗口” > “JSON 大纲”可找到“JSON 大纲”窗口。
3. 若要向模板文件添加资源，可单击“JSON 大纲”窗口顶部的“添加资源”按钮，或右键单击“资源”，然后选择“添加新资源”。

    ![JSON 大纲](./media/logic-apps-deploy-from-vs/jsonoutline.png)
    
4. 在“添加资源”对话框框中浏览并选择“逻辑应用”、为其命名并选择“添加”。

    ![添加资源](./media/logic-apps-deploy-from-vs/addresource.png)

## <a name="next-steps"></a>后续步骤
* 若要开始使用逻辑应用，请按照[创建逻辑应用](logic-apps-create-a-logic-app.md)教程进行操作。  
* [查看常见示例和方案](logic-apps-examples-and-scenarios.md)
* [使用逻辑应用可以自动执行业务流程](http://channel9.msdn.com/Events/Build/2016/T694) 
* [了解如何将系统与逻辑应用集成](http://channel9.msdn.com/Events/Build/2016/P462)




<!--HONumber=Jan17_HO3-->


