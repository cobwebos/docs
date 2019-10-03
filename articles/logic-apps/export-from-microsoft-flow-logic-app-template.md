---
title: 将流从 Microsoft Flow 导出到 Azure 逻辑应用
description: 通过导出为 Azure 资源管理器模板, 将流从 Microsoft Flow 迁移到 Azure 逻辑应用
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 82c4e55eff36a7da70e0304fc8152491a8030e04
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68441022"
---
# <a name="export-flows-from-microsoft-flow-and-deploy-to-azure-logic-apps"></a>从 Microsoft Flow 导出流并将其部署到 Azure 逻辑应用

若要扩展和扩展流的功能, 可以将该流从[Microsoft Flow](https://flow.microsoft.com)迁移到[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)。 可以将流导出为逻辑应用的 Azure 资源管理器模板, 将该逻辑应用模板部署到 Azure 资源组, 然后在逻辑应用设计器中打开该逻辑应用。

> [!NOTE]
> 并非所有 Microsoft Flow 连接器都可用于 Azure 逻辑应用。 可以在 Azure 逻辑应用中导入具有[等效连接器](../connectors/apis-list.md)的流。 例如, 按钮触发器、审批连接器和通知连接器特定于 Microsoft Flow。
>
> 当前不支持将基于 OpenAPI 的流从 Microsoft Flow 导出为逻辑应用模板。 

## <a name="prerequisites"></a>系统必备

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 要从中导出的流 Microsoft Flow

## <a name="export-a-flow"></a>导出流

1. 登录到[Microsoft Flow](https://flow.microsoft.com), 然后选择 **"我的流**"。 查找并选择你的流。 在工具栏上, 选择省略号 ( **...** ) 按钮。 选择 "**导出** > **逻辑应用模板" (json)** 。

   ![导出流](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. 将模板保存到所需的位置。

有关详细信息, 请参阅[增长到 Azure 逻辑应用](https://flow.microsoft.com/blog/grow-up-to-logic-apps/)。

## <a name="deploy-template-by-using-the-azure-portal"></a>使用 Azure 门户部署模板

1. 用 Azure 帐户登录[Azure 门户](https://portal.azure.com)。

1. 在 Azure 主菜单上选择“创建资源”。 在搜索框中, 输入 "模板部署"。 选择**模板部署 (使用自定义模板部署)** , 然后选择 "**创建**"。

   ![选择 "模板部署"](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. 在 "**自定义部署**" 下, 选择 **"在编辑器中生成自己的模板"** 。

   ![选择 "在编辑器中生成自己的模板"](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. 在 "**编辑模板**" 工具栏中, 选择 "**加载文件**"。 查找并选择从 Microsoft Flow 导出的 JSON 模板, 然后选择 "**打开**"。

   ![选择 "加载文件"](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. 在编辑器显示模板中的 JSON、参数和资源后, 选择 "**保存**"。
  
   ![保存模板](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. 现在为模板指定以下输入参数:

   * 用于计费的 Azure 订阅
   * Azure 资源组
   * Azure 资源组的位置
   * 逻辑应用资源的名称
   * 逻辑应用资源的位置 (如果不同于 Azure 资源组)
   * 逻辑应用可重复使用的任何以前创建的连接的名称

      如果要创建第一个逻辑应用, 则所有连接都将创建为新连接, 以便可以接受默认名称。 否则, 你可以指定先前创建的连接的名称, 你可以跨多个逻辑应用使用这些连接。

   为模板提供此信息后, 请查看并同意 Azure Marketplace 用于创建必要的 Azure 资源和对 Azure 订阅进行相应计费的条款和条件, 然后选择 "**购买**"。
  
   ![指定模板的输入参数](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   Azure 会将模板作为逻辑应用部署到指定的资源组。 从 Microsoft Flow 迁移的所有逻辑应用都部署为禁用状态。

1. 激活逻辑应用之前, 请按照以下步骤授权任何新连接:

   1. 打开创建的逻辑应用。 在逻辑应用的菜单中, 选择 "**逻辑应用设计器**"。

      需要授权的每个连接都将显示一个警告图标:

      ![警告图标](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. 对于需要授权连接的每个步骤, 展开该步骤, 然后选择 "**添加新**项"。

      ![添加新连接](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. 登录到每个服务或提供必需的凭据来授权连接。

1. 保存逻辑应用。 如果已准备好激活逻辑应用, 请在逻辑应用的菜单中选择 "**概述**", 然后选择 "**启用**"。

   ![启用逻辑应用](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. 若要避免运行重复的工作流, 请确保停用或删除原始流。

## <a name="deploy-template-by-using-visual-studio"></a>使用 Visual Studio 部署模板

如果已设置 Visual Studio 并具有创建逻辑应用的[先决条件](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites), 则可以将导出的模板从 Visual studio 部署到 Azure 逻辑应用。

1. 在 Visual Studio 中, 打开从 Microsoft Flow 导出的模板文件。

1. 在 Visual Studio 中, 创建一个 Azure 资源组项目, 并按照快速入门中[的步骤选择**逻辑应用**模板:在 Azure 逻辑应用中创建自动化任务、进程和工作流-Visual](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)Studio, 例如:

   ![创建 Azure 资源组项目](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. 如果文件尚未打开, 请从解决方案资源管理器打开**逻辑应用**文件。

1. 从导出的模板复制内容, 并覆盖**逻辑应用**文件中的内容。

1. 在部署逻辑应用之前, 请按照以下步骤授权任何新连接:

   1. 打开 "**逻辑应用**" 快捷菜单, 然后选择 "**通过逻辑应用设计器打开**"。

      ![在逻辑应用设计器中打开模板](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. 如果系统提示, 请选择要用于部署逻辑应用的 Azure 订阅和资源组。

      ![选择 Azure 订阅和资源组](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      逻辑应用出现在设计器中之后, 任何需要授权的连接都将显示警告图标:

      ![带有警告图标的连接](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. 对于需要授权连接的每个步骤, 展开该步骤, 然后选择 "**添加新**项"。

      ![添加新连接](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. 登录到每个服务或提供必需的凭据来授权连接。

   1. 在部署逻辑应用之前保存解决方案。

1. 在解决方案资源管理器中, 打开项目快捷菜单, 然后选择 "**部署** > **新**的"。 如果出现系统提示，请使用 Azure 帐户登录。

1. 出现提示时, 确认要用于部署的 Azure 订阅、Azure 资源组和其他任何设置, 如用于传递模板参数值的[参数文件](../azure-resource-manager/resource-group-template-deploy.md#parameter-files), 然后选择 "**部署**"。

   ![确认部署设置](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. 如果显示 "**编辑参数**" 框, 请在 Azure 中提供逻辑应用资源的名称, 然后选择 "**保存**"。  

   ![编辑部署参数](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   部署开始时，应用的部署状态显示在 Visual Studio 的“输出”窗口中。 如果状态不显示，请打开“显示输出来源”列表，然后选择 Azure 资源组。 例如：

   ![输出窗口](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   如果逻辑应用中的任何连接需要你输入, 则会在后台打开一个 PowerShell 窗口, 并提示你提供所需的密码或密钥。 输入该信息后，部署会继续。

   ![验证连接](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   部署完成后, 将发布逻辑应用, 但不会在 Azure 门户中激活。

1. 如果已准备好在 Azure 门户中激活逻辑应用, 请在逻辑应用设计器中查找并打开逻辑应用。 在逻辑应用的菜单上, 选择 "**概述**", 然后选择 "**启用**"。

1. 若要避免运行重复的工作流, 请确保停用或删除原始流。

有关这些部署步骤的详细信息, 请[参阅快速入门:通过 Azure 逻辑应用创建自动化任务、进程和工作流-Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure)

## <a name="next-steps"></a>后续步骤

* 详细了解[Azure 逻辑应用的连接器](../connectors/apis-list.md)
* 详细了解 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)
