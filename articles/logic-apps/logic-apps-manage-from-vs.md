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
ms.openlocfilehash: 34ce65ec272c9fe5be8f95371179de092c5df2f3
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="manage-your-logic-apps-with-visual-studio-cloud-explorer"></a>使用 Visual Studio Cloud Explorer 管理逻辑应用

尽管 [Azure 门户](https://portal.azure.com/)提供了设计和管理 Azure 逻辑应用的极佳方法，但可以使用 Visual Studio Cloud Explorer 管理包括逻辑应用在内的许多 Azure 资产。 Visual Studio Cloud Explorer 支持浏览、管理、编辑和下载已发布的逻辑应用。 管理任务包括启用、禁用和查看运行历史记录。 

如需访问和管理 Visual Studio 中的逻辑应用，请为 Azure 逻辑应用安装和配置这些 Visual Studio 工具。 

## <a name="prerequisites"></a>先决条件

* [Visual Studio 2015 或 Visual Studio 2017](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [最新版 Azure SDK](https://azure.microsoft.com/downloads/)（2.9.1 或更高版本）
* [Visual Studio Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)
* 使用嵌入式设计器访问 Web

## <a name="install-visual-studio-tools-for-logic-apps"></a>安装用于逻辑应用的 Visual Studio 工具

安装必备组件后，下载并安装适用于 Visual Studio 的 Azure 逻辑应用工具。

1. 打开 Visual Studio。 在“工具”菜单上，选择“扩展和更新”。
2. 展开“联机”类别以便在 Visual Studio Gallery 中进行联机搜索。
3. 浏览或搜索“逻辑应用”，直至找到“适用于 Visual Studio 的 Azure 逻辑应用工具”。
4. 若要下载和安装扩展，请单击“下载”。
5. 安装后重启 Visual Studio。

> [!NOTE]
> 若要下载适用于 Visual Studio 的 Azure 逻辑应用工具，请转到 [Visual Studio Marketplace](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9)。

## <a name="browse-for-logic-apps-in-cloud-explorer"></a>在 Cloud Explorer 中浏览逻辑应用

1.  若要打开 Cloud Explorer，请在“查看”菜单上选择“Cloud Explorer”。
2.  按资源组或资源类型浏览逻辑应用。 

    * 如果按资源类型浏览，请选择 Azure 订阅，展开“逻辑应用”部分，并选择逻辑应用。 
    * 如果按资源组浏览，请展开有逻辑应用的资源组，并选择逻辑应用。

    若要查看逻辑应用的命令，请右键单击逻辑应用，或者在 Cloud Explorer 底部，从“操作”菜单进行选择。

    ![浏览逻辑应用](./media/logic-apps-manage-from-vs/browse.png)

## <a name="edit-your-logic-app-with-logic-apps-designer"></a>在逻辑应用设计器中编辑逻辑应用

从 Cloud Explorer，可在 Azure 门户中使用的同一设计器中打开当前部署的逻辑应用。 

* 若要编辑逻辑应用，在 Cloud Explorer 中，右键单击逻辑应用，然后选择“在逻辑应用编辑器中打开”。 

* 要将更新发布到云，请选择“发布”。 

* 要启动新的运行，请选择“运行触发器”。

![逻辑应用设计器](./media/logic-apps-manage-from-vs/designer.png)

还可从设计器下载逻辑应用。 此操作会自动参数化逻辑应用定义，并将定义保存为 Azure 资源管理器部署模板。 可将此部署模板添加到 Azure 资源组项目。

## <a name="browse-your-logic-app-run-history"></a>浏览逻辑应用运行历史记录

要查看逻辑应用的运行历史记录，请右键单击逻辑应用，并选择“打开运行历史记录”。 若要按所显示的任何属性对运行历史记录重新排序，请选择列标题。

![运行历史记录](media/logic-apps-manage-from-vs/runs.png)

要显示实例的运行历史记录，以便查看运行结果（包括每个步骤的输入和输出），请双击其中一个运行实例。

![运行历史记录结果、各步骤的输入和输出](./media/logic-apps-manage-from-vs/history.png)

## <a name="next-steps"></a>后续步骤

* [创建第一个逻辑应用](quickstart-create-first-logic-app-workflow.md)
* [在 Visual Studio 中设计、生成和部署逻辑应用](logic-apps-deploy-from-vs.md)
* [查看常见示例和方案](logic-apps-examples-and-scenarios.md)
* [视频：使用 Azure 逻辑应用自动执行业务流程](http://channel9.msdn.com/Events/Build/2016/T694)
* [视频：将系统与 Azure 逻辑应用集成](http://channel9.msdn.com/Events/Build/2016/P462)
