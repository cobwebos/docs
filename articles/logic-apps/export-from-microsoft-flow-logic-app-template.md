---
title: 将流从电源自动执行导出到 Azure 逻辑应用
description: 通过导出为 Azure 资源管理器模板，将流从电源自动迁移到 Azure 逻辑应用
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: b2a45218118282f4f1cadb29c9022fc05b30f907
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87078733"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>从 Power Automate 导出流并将其部署到 Azure 逻辑应用

若要扩展和扩展流的功能，可以将该流从[电源自动](https://flow.microsoft.com)迁移到[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)。 可以将流导出为逻辑应用的 Azure 资源管理器模板，将该逻辑应用模板部署到 Azure 资源组，然后在逻辑应用设计器中打开该逻辑应用。

> [!NOTE]
> 在 Azure 逻辑应用中，并不是所有电源自动连接器都可用。 只能迁移在 Azure 逻辑应用中具有等效连接器的电源自动执行流。 例如，按钮触发器、审批连接器和通知连接器特定于电源自动执行。 目前，OpenAPI 中基于的流不支持作为逻辑应用模板的导出和部署。
>
> * 若要找出哪些电源自动连接器没有逻辑应用等效项，请参阅[电源自动连接器](/connectors/connector-reference/connector-reference-powerautomate-connectors)。
>
> * 若要找出哪些逻辑应用连接器没有电源自动执行等效功能，请参阅[逻辑应用连接器](/connectors/connector-reference/connector-reference-powerautomate-connectors)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 要从电源自动导出的流

## <a name="export-your-flow"></a>导出流

1. 登录到 "[电源自动](https://flow.microsoft.com)"，然后选择 **"我的流**"。 查找并选择你的流。 在工具栏上，选择 "省略号" （**...**）按钮 >**导出**  >  **逻辑应用模板（json）**。

   ![从电源自动执行导出流程](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. 将模板的 json 文件保存到所需的位置。

有关详细信息，请参阅[增长到 Azure 逻辑应用](https://flow.microsoft.com/blog/grow-up-to-logic-apps/)。

## <a name="deploy-template-by-using-the-azure-portal"></a>使用 Azure 门户部署模板

1. 用 Azure 帐户登录[Azure 门户](https://portal.azure.com)。

1. 在 Azure 主页上的搜索框中，输入 `custom template` 。 在结果中，选择 "**部署自定义模板**  >  **创建**"。

   ![查找并选择 "模板部署"](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. 在 "**自定义部署**" 下，选择 **"在编辑器中生成自己的模板"**。

   ![选择 "在编辑器中生成自己的模板"](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. 在 "**编辑模板**" 工具栏上，选择 "**加载文件**"。

   ![选择 "加载文件"](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. 浏览到保存从 "Power 自动" 导出的 JSON 模板文件的位置。 选择 >**打开**的模板文件。

1. 在编辑器显示模板中的 JSON、参数和资源后，选择 "**保存**"。

   ![保存模板](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. 现在提供逻辑应用的详细信息。

   1. 选择或指定模板的输入参数值。

      | properties | 描述 |
      |----------|-------------|
      | **订阅** | 用于计费的 Azure 订阅 |
      | **资源组** | 要用于逻辑应用的 Azure 资源组。 您可以使用现有组或创建新组。 |
      | **位置** | 创建新的资源组时要使用的 Azure 区域 |
      | **逻辑应用名称** | 逻辑应用资源使用的名称 |
      | **逻辑应用位置** | 要在其中创建逻辑应用资源的 Azure 区域（如果不同于 Azure 资源组） |
      | <*connection-name*> | 逻辑应用可重复使用的任何以前创建的连接的一个或多个名称 <p><p>**注意**：如果此逻辑应用是第一个，则会将所有连接创建为新连接，以便可以接受默认名称。 否则，你可以指定先前创建的连接的名称，你可以跨多个逻辑应用使用这些连接。 |
      |||

      例如：

      ![指定模板的输入参数](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   1. 完成后，请查看有关创建必要的 Azure 资源的**条款和条件**，并相应地计费 azure 订阅。

   1. 准备就绪后，选择 **"我同意上述条款和条件"**  >  **Purchase**。

      Azure 会将模板作为逻辑应用部署到指定的资源组。

1. 从电源自动执行迁移的所有逻辑应用都部署在禁用状态。 启用逻辑应用之前，请按照以下步骤授权任何新连接：

   1. 在 Azure 门户中，打开创建的逻辑应用。 在逻辑应用的菜单中，选择 "**逻辑应用设计器**"。

      需要授权的每个连接都将显示一个警告图标：

      ![警告图标](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. 对于需要授权连接的每个步骤，展开该步骤，然后选择 "**添加新**项"。

      ![添加新连接](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. 登录到每个服务或提供必需的凭据来授权连接。

   1. 更新连接后，请在设计器工具栏上选择 "**保存**"。

1. 如果已准备好激活逻辑应用，请在逻辑应用的菜单中选择 "**概述**"，然后选择 "**启用**"。

   ![启用逻辑应用](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. 若要避免运行重复的工作流，请确保停用或删除原始流。

## <a name="deploy-template-by-using-visual-studio"></a>使用 Visual Studio 部署模板

如果已设置 Visual Studio 并具有创建逻辑应用的[先决条件](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites)，则可以使用 Visual Studio 将导出的模板部署到 Azure 逻辑应用。

1. 在 Visual Studio 中，浏览到并打开从 Power 自动功能导出的逻辑应用模板的 json 文件。

1. 在 Visual Studio 中，按照[快速入门：使用 Azure 逻辑应用创建自动化任务、进程和工作流-Visual studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)中的步骤创建一个使用**逻辑应用**模板的**Azure 资源组**项目。

   此示例创建一个名为 "ImportedLogicApp" 的 Visual Studio 解决方案。

   ![创建 Azure 资源组项目](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. 创建解决方案后，在解决方案资源管理器中打开文件的**LogicApp.js** （如果该文件尚未打开）。

1. 从导出的模板复制内容，并覆盖**LogicApp.js**文件中的内容。

1. 在部署逻辑应用之前，请按照以下步骤授权任何新连接：

   1. 在快捷菜单上打开 " **LogicApp.js** "，然后选择 "**用逻辑应用设计器打开**"。

      ![在逻辑应用设计器中打开模板](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. 如果系统提示，请选择要用于部署逻辑应用的 Azure 订阅和资源组。

      ![选择 Azure 订阅和资源组](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      逻辑应用出现在设计器中之后，任何需要授权的连接都将显示警告图标：

      ![带有警告图标的连接](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. 对于需要授权连接的每个步骤，展开该步骤，然后选择 "**添加新**项"。

      ![添加新连接](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. 登录到每个服务或提供必需的凭据来授权连接。

   1. 在部署逻辑应用之前保存解决方案。

1. 在解决方案资源管理器中，打开项目快捷菜单，然后选择 "**部署**  >  **新**的"。 如果出现系统提示，请使用 Azure 帐户登录。

1. 出现提示时，确认要用于部署的 Azure 订阅、Azure 资源组和其他任何设置，如用于传递模板参数值的[参数文件](../azure-resource-manager/templates/parameter-files.md)，然后选择 "**部署**"。

   ![确认部署设置](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. 如果显示 "**编辑参数**" 框，请在 Azure 中提供逻辑应用资源的名称，然后选择 "**保存**"。  

   ![编辑部署参数](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   部署开始时，应用的部署状态显示在 Visual Studio 的“输出”**** 窗口中。 如果状态不显示，请打开“显示输出来源”**** 列表，然后选择 Azure 资源组。 例如：

   ![“输出”窗口](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   如果逻辑应用中的任何连接需要你输入，则会在后台打开一个 PowerShell 窗口，并提示你提供所需的密码或密钥。 输入该信息后，部署会继续。

   ![身份验证连接](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   部署完成后，将发布逻辑应用，但不会在 Azure 门户中激活。

1. 如果已准备好在 Azure 门户中激活逻辑应用，请在逻辑应用设计器中查找并打开逻辑应用。 在逻辑应用的菜单上，选择 "**概述**"，然后选择 "**启用**"。

1. 若要避免运行重复的工作流，请确保停用或删除原始流。

有关这些部署步骤的详细信息，请参阅[快速入门：通过 Azure 逻辑应用创建自动化任务、进程和工作流-Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure)

## <a name="next-steps"></a>后续步骤

* 详细了解[Azure 逻辑应用的连接器](../connectors/apis-list.md)
* 了解有关[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)的详细信息
