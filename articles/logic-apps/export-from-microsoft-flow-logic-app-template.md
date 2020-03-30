---
title: 将流量从"电源自动"导出到 Azure 逻辑应用
description: 通过导出为 Azure 资源管理器模板，将流从"电源自动"迁移到 Azure 逻辑应用
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 616f10b32d0a9c1a05d759a0e27550cd2808808b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75428884"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>从 Power Automate 导出流并将其部署到 Azure 逻辑应用

要扩展和扩展流的功能，可以将该流从["电源自动"](https://flow.microsoft.com)迁移到 Azure[逻辑应用](../logic-apps/logic-apps-overview.md)。 您可以将流导出为逻辑应用的 Azure 资源管理器模板，将该逻辑应用模板部署到 Azure 资源组，然后在逻辑应用设计器中打开该逻辑应用。

> [!NOTE]
> 并非所有电源自动连接器在 Azure 逻辑应用中都可用。 可以导入 Azure 逻辑应用中具有[等效连接器](../connectors/apis-list.md)的流。 例如，"按钮"触发器、审批连接器和通知连接器特定于"电源自动"。
>
> 当前不支持从 Power Automate 导出的基于 OpenAPI 的流作为逻辑应用模板进行部署。 

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 要从电源自动功能输出的流

## <a name="export-a-flow"></a>导出流

1. 登录到[电源自动 ，](https://flow.microsoft.com)然后选择 **"我的流**"。 查找并选择您的流。 在工具栏上，选择椭圆 （**...**） 按钮。 选择**导出** > **逻辑应用模板 （.json）。**

   ![导出流](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. 将模板保存到所需的位置。

有关详细信息，请参阅["长到 Azure 逻辑应用](https://flow.microsoft.com/blog/grow-up-to-logic-apps/)"。

## <a name="deploy-template-by-using-the-azure-portal"></a>使用 Azure 门户部署模板

1. 使用 Azure 帐户在[Azure 门户](https://portal.azure.com)中签名。

1. 在 Azure 主菜单上选择“创建资源”****。 在搜索框中，输入"模板部署"。 选择**模板部署（使用自定义模板部署），** 然后选择 **"创建**"。

   ![选择"模板部署"](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. 在 **"自定义部署"** 下，在**编辑器中选择"构建您自己的模板**"。

   ![选择"在编辑器中构建您自己的模板"](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. 在 **"编辑模板**"工具栏中，选择 **"加载文件**"。 查找并选择从"电源自动"导出的 JSON 模板，然后选择 **"打开**"。

   ![选择"加载文件"](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. 编辑器在模板中显示 JSON、参数和资源后，选择 **"保存**"。
  
   ![保存模板](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. 现在为模板指定这些输入参数：

   * 用于计费的 Azure 订阅
   * Azure 资源组
   * Azure 资源组的位置
   * 逻辑应用资源的名称
   * 逻辑应用资源的位置（如果与 Azure 资源组不同）
   * 逻辑应用可以重用的任何以前创建的连接的名称

      如果要创建第一个逻辑应用，则所有连接都作为新连接创建，因此可以接受默认名称。 否则，您可以指定以前创建的连接的名称，这些连接可以在多个逻辑应用中使用。

   为模板提供此信息后，查看并同意 Azure 应用商店条款和条件，以便创建必要的 Azure 资源并相应地向 Azure 订阅计费，然后选择 **"购买**"。
  
   ![为模板指定输入参数](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   Azure 将模板作为逻辑应用部署到指定的资源组。 从 Power 自动迁移的所有逻辑应用都部署在禁用状态。

1. 在激活逻辑应用之前，请按照以下步骤授权任何新连接：

   1. 打开您创建的逻辑应用。 在逻辑应用的菜单上，选择**逻辑应用设计器**。

      需要授权的每个连接都显示一个警告图标：

      ![警告图标](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. 对于需要授权连接的每个步骤，请展开该步骤，然后选择"**添加新**"。

      ![添加新连接](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. 登录到每个服务或提供必要的凭据以授权连接。

1. 保存逻辑应用。 准备好激活逻辑应用时，请在逻辑应用的菜单上选择 **"概述****"，** 然后选择"启用 "。

   ![启用逻辑应用](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. 为避免运行重复工作流，请确保停用或删除原始流。

## <a name="deploy-template-by-using-visual-studio"></a>使用可视化工作室部署模板

如果已使用创建逻辑应用[的先决条件](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites)设置 Visual Studio，则可以将导出的模板从可视化工作室部署到 Azure 逻辑应用。

1. 在可视化工作室中，打开从 Power 自动执行导出的模板文件。

1. 在可视化工作室中，创建 Azure 资源组项目，然后按照["快速入门：使用 Azure 逻辑应用 - 可视化工作室"创建自动化任务、流程和工作流](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)的步骤选择**逻辑应用**模板， 例如：

   ![创建 Azure 资源组项目](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. 从解决方案资源管理器，打开**LogicApp.json**文件，如果文件尚未打开。

1. 从导出的模板复制内容，并在**LogicApp.json**文件中覆盖内容。

1. 在部署逻辑应用之前，请按照以下步骤授权任何新连接：

   1. 打开**LogicApp.json**快捷菜单，然后选择 **"使用逻辑应用设计器打开**"。

      ![使用逻辑应用设计器打开模板](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. 如果出现提示，请选择要用于部署逻辑应用的 Azure 订阅和资源组。

      ![选择 Azure 订阅和资源组](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      逻辑应用在设计器中显示后，任何需要授权的连接都显示警告图标：

      ![具有警告图标的连接](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. 对于需要授权连接的每个步骤，请展开该步骤，然后选择"**添加新**"。

      ![添加新连接](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. 登录到每个服务或提供必要的凭据以授权连接。

   1. 在部署逻辑应用之前保存解决方案。

1. 在解决方案资源管理器中，打开项目快捷方式菜单，然后选择 **"部署** > **新建**"。 如果出现系统提示，请使用 Azure 帐户登录。

1. 提示后，确认 Azure 订阅、Azure 资源组以及要用于部署的任何其他设置，例如用于传递模板参数值[的参数文件](../azure-resource-manager/templates/parameter-files.md)，然后选择 **"部署**"。

   ![确认部署设置](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. 如果出现 **"编辑参数"** 框，请在 Azure 中提供逻辑应用资源的名称，然后选择"**保存**"。  

   ![编辑部署参数](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   部署开始时，应用的部署状态显示在 Visual Studio 的“输出”**** 窗口中。 如果状态不显示，请打开“显示输出来源”**** 列表，然后选择 Azure 资源组。 例如：

   ![输出窗口](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   如果逻辑应用中的任何连接需要您输入，则 PowerShell 窗口将在后台打开，并提示输入任何必要的密码或密钥。 输入该信息后，部署会继续。

   ![身份验证连接](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   部署完成后，逻辑应用将发布，但不会在 Azure 门户中激活。

1. 准备好在 Azure 门户中激活逻辑应用后，在逻辑应用设计器中查找并打开逻辑应用。 在逻辑应用的菜单上，选择 **"概述**"，然后选择"**启用**"。

1. 为避免运行重复工作流，请确保停用或删除原始流。

有关这些部署步骤的详细信息，请参阅[快速入门：使用 Azure 逻辑应用创建自动化任务、流程和工作流 - 可视化工作室](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure)

## <a name="next-steps"></a>后续步骤

* 了解有关[Azure 逻辑应用连接器](../connectors/apis-list.md)的更多信息
* 了解有关[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)的更多信息
