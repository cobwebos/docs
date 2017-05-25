---
title: "在 Visual Studio 中管理逻辑应用 — Azure 逻辑应用 | Microsoft Docs"
description: "使用 Visual Studio Cloud Explorer 管理逻辑应用和其他 Azure 资产"
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
ms.custom: H1Hack27Feb2017
ms.date: 12/19/2016
ms.author: LADocs; klam
ms.translationtype: Human Translation
ms.sourcegitcommit: 2fafb3c127183c4903ed7ee0da5811988db9116c
ms.openlocfilehash: 9a542f83eb35b159de04f0395cd850376da498de
ms.contentlocale: zh-cn
ms.lasthandoff: 03/02/2017

---

# <a name="manage-your-logic-apps-with-visual-studio-cloud-explorer"></a>使用 Visual Studio Cloud Explorer 管理逻辑应用

尽管 [Azure 门户](https://portal.azure.com/)提供了设计和管理 Azure 逻辑应用的极佳方法，但使用 Visual Studio Cloud Explorer 可以在 Visual Studio 中管理包括逻辑应用在内的许多 Azure 资产。 你可以浏览已发布的逻辑应用，执行启用和禁用逻辑应用或者编辑和查看运行历史记录等任务。 

## <a name="installation-steps"></a>安装步骤

若要安装和配置用于 Azure 逻辑应用的 Visual Studio 工具，请按照以下步骤进行操作。

### <a name="prerequisites"></a>先决条件

* [Visual Studio 2015 或 Visual Studio 2017](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [最新版 Azure SDK](https://azure.microsoft.com/downloads/)（2.9.1 或更高版本）
* [Visual Studio Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)
* 使用嵌入式设计器访问 Web

### <a name="install-visual-studio-tools-for-logic-apps"></a>安装用于逻辑应用的 Visual Studio 工具

安装必备组件后，请执行以下操作：

1. 打开 Visual Studio。 在“工具”菜单上，选择“扩展和更新”。
2. 展开“联机”类别以在线搜索。
3. 浏览或搜索“逻辑应用”，直至找到“适用于 Visual Studio 的 Azure 逻辑应用工具”。
4. 若要下载和安装扩展，请单击“下载”。
5. 安装后重启 Visual Studio。

> [!NOTE]
> 也可以直接从 [Visual Studio Marketplace](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9)下载适用于 Visual Studio 的 Azure 逻辑应用工具。

## <a name="browse-for-logic-apps-in-cloud-explorer"></a>在 Cloud Explorer 中浏览逻辑应用

1.    若要打开 Cloud Explorer，请在“查看”菜单上选择“Cloud Explorer”。
2.    按资源组或资源类型浏览逻辑应用。 

    如果按资源类型浏览，请选择 Azure 订阅，展开“逻辑应用”部分，然后选择一个逻辑应用。 
    可以右键单击逻辑应用，也可以从 Cloud Explorer 底部的“操作”菜单中进行选择。

    ![浏览逻辑应用](./media/logic-apps-manage-from-vs/browse.png)

## <a name="edit-your-logic-app-with-logic-app-designer"></a>在逻辑应用设计器中编辑逻辑应用

若要在 Azure 门户所用的设计器中打开当前部署的逻辑应用，请右键单击逻辑应用，然后选择“使用逻辑应用编辑器打开”。 

在设计器中，可以编辑逻辑应用并将更新保存到云中，然后通过选择“运行触发器”启动新的运行。

![逻辑应用设计器](./media/logic-apps-manage-from-vs/designer.png)

## <a name="browse-your-logic-app-run-history"></a>浏览逻辑应用运行历史记录

若要查看逻辑应用的运行历史记录，请右键单击逻辑应用，然后选择“打开运行历史记录”。 若要按所显示的任何属性对运行历史记录重新排序，请选择列标题。

![运行历史记录](media/logic-apps-manage-from-vs/runs.png)

若要显示实例的运行历史记录，以便查看运行结果（包括每个步骤的输入和输出），请双击其中一个运行实例。

![运行历史记录结果、各步骤的输入和输出](./media/logic-apps-manage-from-vs/history.png)

## <a name="next-steps"></a>后续步骤

*    若要开始使用 Azure 逻辑应用，请参阅[如何在 Azure 门户中创建第一个逻辑应用](logic-apps-create-a-logic-app.md)
* [在 Visual Studio 中设计、生成和部署逻辑应用](logic-apps-deploy-from-vs.md)
* [查看常见示例和方案](logic-apps-examples-and-scenarios.md)
* [了解如何使用 Azure 逻辑应用自动执行业务流程](http://channel9.msdn.com/Events/Build/2016/T694)
* [了解如何将系统与 Azure 逻辑应用集成](http://channel9.msdn.com/Events/Build/2016/P462)

