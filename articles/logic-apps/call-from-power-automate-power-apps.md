---
title: 从 Power Automate 和 Power Apps 调用逻辑应用
description: 通过将逻辑应用导出为连接器，从 Microsoft Power 自动流中调用逻辑应用。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: b402dab4c6e94a7634e11f0330b5379315e43abf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91762442"
---
# <a name="call-logic-apps-from-power-automate-and-power-apps"></a>从 Power Automate 和 Power Apps 调用逻辑应用

若要从 Microsoft Power 自动化和 Microsoft Power Apps 调用逻辑应用，可将逻辑应用作为连接器导出。 当你在电源自动执行或电源应用环境中将逻辑应用公开为自定义连接器时，你可以从此处的流中调用逻辑应用。

如果你想要从电源自动执行流或为逻辑应用启动逻辑应用，请参阅 [从电源自动执行导出流和部署到 Azure 逻辑应用](export-from-microsoft-flow-logic-app-template.md)。

> [!NOTE]
> 在 Azure 逻辑应用中，并不是所有电源自动连接器都可用。 只能迁移在 Azure 逻辑应用中具有等效连接器的电源自动执行流。 例如，按钮触发器、审批连接器和通知连接器特定于电源自动执行。 目前，OpenAPI 中基于的流不支持作为逻辑应用模板的导出和部署。
>
> * 若要找出哪些电源自动连接器没有逻辑应用等效项，请参阅 [电源自动连接器](/connectors/connector-reference/connector-reference-powerautomate-connectors)。
>
> * 若要找出哪些逻辑应用连接器没有电源自动执行等效功能，请参阅 [逻辑应用连接器](/connectors/connector-reference/connector-reference-powerautomate-connectors)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 电源自动化或 Power Apps 许可证。

* 具有要导出的请求触发器的逻辑应用。

* 使你要从中调用逻辑应用的电源自动执行或电源应用的流。

## <a name="export-your-logic-app-as-a-custom-connector"></a>将逻辑应用导出为自定义连接器

你必须先将逻辑应用作为自定义连接器导出，然后才能从 Power 自动化或 Power Apps 调用逻辑应用。

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 门户搜索框中输入 `Logic Apps` 。 在结果中，在 " **服务**" 下选择 " **逻辑应用**"。

1. 选择要导出的逻辑应用。

1. 从逻辑应用的菜单中，选择 " **导出**"。

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/export-logic-app.png" alt-text="Azure 门户中逻辑应用的页面屏幕截图，其中显示了 &quot;导出&quot; 按钮处于选中状态的菜单。&quot;:::

1. 在 &quot; **导出** &quot; 窗格的 &quot; **名称**&quot; 中，输入逻辑应用的自定义连接器的名称。 从 &quot; **环境** " 列表中，选择要从其调用逻辑应用的电源自动执行或电源应用环境。 完成后，请选择“确定”。

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/export-logic-app2.png" alt-text="Azure 门户中逻辑应用的页面屏幕截图，其中显示了 &quot;导出&quot; 按钮处于选中状态的菜单。&quot;:::

1. 在 &quot; **导出** &quot; 窗格的 &quot; **名称**&quot; 中，输入逻辑应用的自定义连接器的名称。 从 &quot; **环境** " 窗格。

### <a name="exporting-errors"></a>导出错误

以下是将逻辑应用导出为自定义连接器及其建议解决方案时可能会发生的错误：

* **未能获取环境。请确保已将你的帐户配置为使用自动电源，然后重试。**：检查你的 Azure 帐户是否具有电源自动化计划。

* **无法导出当前逻辑应用。若要导出，请选择具有请求触发器的逻辑应用。**：检查逻辑应用是否以 [请求触发器](./logic-apps-workflow-actions-triggers.md#request-trigger)开头。

## <a name="connect-to-your-logic-app-from-power-automate"></a>从电源自动连接到逻辑应用

若要连接到通过电源自动流导出的逻辑应用，请执行以下操作：

1. 登录到 [Power Automate](https://flow.microsoft.com)。

1. 从 " **自动启动** " 主页菜单中，选择 " **我的流**"。

1. 在 " **流** " 页上，选择要连接到逻辑应用的流。

1. 从流页的菜单中，选择 " **编辑**"。

1. 在流编辑器中，选择 " **&#43;" 新建步骤**"。

1. 在 " **选择操作**" 下的 "搜索" 框中，输入逻辑应用连接器的名称。 或者，若要仅显示您的环境中的自定义连接器，请通过选择 " **自定义** " 选项卡来筛选结果。

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/power-automate-custom-connector-action.png" alt-text="Azure 门户中逻辑应用的页面屏幕截图，其中显示了 &quot;导出&quot; 按钮处于选中状态的菜单。&quot;:::

1. 在 &quot; **导出** &quot; 窗格的 &quot; **名称**&quot; 中，输入逻辑应用的自定义连接器的名称。 从 &quot; **环境** " **保存**"。

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在逻辑应用服务中，找到已导出的逻辑应用。

1. 确认逻辑应用的工作方式与您在电源自动流程中预期的一样。

## <a name="delete-logic-app-connector-from-power-automate"></a>从电源自动执行删除逻辑应用连接器

1. 登录到 [Power Automate](https://flow.microsoft.com)。

1. 在 " **自动启动** " 主页上，选择菜单中的 " **数据** &gt; **自定义连接器** "。

1. 在列表中找到自定义连接器，然后选择 " (**...** ") 按钮 " &gt; **删除**"。

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/delete-custom-connector.png" alt-text="Azure 门户中逻辑应用的页面屏幕截图，其中显示了 &quot;导出&quot; 按钮处于选中状态的菜单。&quot;:::

1. 在 &quot; **导出** &quot; 窗格的 &quot; **名称**&quot; 中，输入逻辑应用的自定义连接器的名称。 从 &quot; **环境** ":::

1. 若要确认删除，请选择 **"确定"**。

## <a name="connect-to-your-logic-app-from-power-apps"></a>从 Power Apps 连接到逻辑应用

若要连接到通过 Power Apps 流导出的逻辑应用，请执行以下操作：

1. 登录到 [Power Apps](https://powerapps.microsoft.com/)。

1. 在 " **电源应用** " 主页上，选择菜单中的 " **流** "。

1. 在 " **流** " 页上，选择要连接到逻辑应用的流。

1. 在流的页面上，选择流的菜单中的 " **编辑** "。

1. 在流编辑器中，选择 " **&#43; 新步骤** " 按钮。

1. 在 "新步骤" 中的 " **选择操作** " 下，在 "搜索" 框中输入逻辑应用连接器的名称。 根据需要，通过 " **自定义** " 选项卡筛选结果，以便只查看环境中的自定义连接器。

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/power-apps-custom-connector-action.png" alt-text="Azure 门户中逻辑应用的页面屏幕截图，其中显示了 &quot;导出&quot; 按钮处于选中状态的菜单。&quot;:::

1. 在 &quot; **导出** &quot; 窗格的 &quot; **名称**&quot; 中，输入逻辑应用的自定义连接器的名称。 从 &quot; **环境** " 以保存所做的更改。 

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在逻辑应用服务中，找到已导出的逻辑应用。

1. 确认你的逻辑应用按预期方式在你的 Power Apps 流中运行。

## <a name="delete-logic-app-connector-from-power-apps"></a>从电源应用删除逻辑应用连接器

1. 登录到 [Power Apps](https://powerapps.microsoft.com)。

1. 在 " **电源应用** " 主页上，选择菜单中的 " **数据** &gt; **自定义连接器** "。

1. 在列表中找到自定义连接器，然后选择 " (**...** ") 按钮 " &gt; **删除**"。

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/delete-custom-connector.png" alt-text="Azure 门户中逻辑应用的页面屏幕截图，其中显示了 &quot;导出&quot; 按钮处于选中状态的菜单。&quot;:::

1. 在 &quot; **导出** &quot; 窗格的 &quot; **名称**&quot; 中，输入逻辑应用的自定义连接器的名称。 从 &quot; **环境** ":::

1. 若要确认删除，请选择 **"确定"**。

## <a name="next-steps"></a>后续步骤

* 详细了解 [Azure 逻辑应用的连接器](../connectors/apis-list.md)
* 了解有关[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)的详细信息
