---
title: "通过 Visual Studio Cloud Explorer 管理 Azure 逻辑应用 | Microsoft Docs"
description: "通过 Visual Studio Cloud Explorer 管理 Azure 逻辑应用。"
author: klam
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: klam
translationtype: Human Translation
ms.sourcegitcommit: a9f786259676a1bc9aa616f2db2935dff45475e2
ms.openlocfilehash: dcb0512b4f9e6c94262a3f39ef59f82fcfbf313e


---
# <a name="manage-logic-apps-with-the-visual-studio-cloud-explorer"></a>使用 Visual Studio Cloud Explorer 管理逻辑应用
尽管 [Azure 门户](https://portal.azure.com)提供了一种设计和管理逻辑应用的极佳方法，但 Visual Studio Cloud Explorer 则可允许在 Visual Studio 内管理包括逻辑应用在内的众多 Azure 资产。  通过 Cloud Explorer 可浏览已发布的逻辑应用，执行启用、禁用、编辑和查看运行历史记录等操作。 

## <a name="installation-steps"></a>安装步骤
下面是安装和配置用于逻辑应用的 Visual Studio 工具的步骤。

### <a name="prerequisites"></a>先决条件
* [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [最新版 Azure SDK](https://azure.microsoft.com/downloads/)（2.9.1 或更高版本）
* [Visual Studio Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)
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

## <a name="browsing-logic-apps"></a>浏览逻辑应用
若要在 Cloud Explorer 中浏览逻辑应用，请通过“查看”>“Cloud Explorer”打开 Cloud Explorer，然后可按资源组或资源类型进行浏览。  如按资源类型进行浏览，请选择一个订阅，展开“逻辑应用”部分，然后选择一个逻辑应用。  可通过右键单击其中一个逻辑应用或使用 Cloud Explorer 底部的“操作”菜单执行所需操作。

![浏览](./media/logic-apps-manage-from-vs/browse.png)

## <a name="edit-logic-app-with-the-designer"></a>使用设计器编辑逻辑应用
若要在处于 Azure 门户中的同一设计器中打开当前已部署的逻辑应用，请右键单击逻辑应用，然后选择“使用逻辑应用编辑器打开”。  通过编辑器可编辑逻辑应用，并将其保存回云中，以及可使用“运行触发器”按钮启动新的运行。

![设计器](./media/logic-apps-manage-from-vs/designer.png)

## <a name="browse-logic-app-run-history"></a>浏览逻辑应用运行历史记录
若要列出逻辑应用的运行历史记录，请右键单击逻辑应用，然后选择“打开运行历史记录”。  在此视图中，通过选择列标题可按任何显示的属性进行排序。  

![运行次数](media/logic-apps-manage-from-vs/runs.png)

双击其中一个运行实例可显示该实例的运行历史记录，在其中可看到运行结果（包括每个步骤的输入和输出）。

![历史记录](./media/logic-apps-manage-from-vs/history.png)

## <a name="next-steps"></a>后续步骤
* 若要开始使用逻辑应用，请按照[创建逻辑应用](logic-apps-create-a-logic-app.md)教程进行操作。  
* [查看常见示例和方案](logic-apps-examples-and-scenarios.md)
* [使用逻辑应用可以自动执行业务流程](http://channel9.msdn.com/Events/Build/2016/T694) 
* [了解如何将系统与逻辑应用集成](http://channel9.msdn.com/Events/Build/2016/P462)




<!--HONumber=Jan17_HO3-->


