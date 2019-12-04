---
title: 使用模拟数据测试逻辑应用
description: 设置用模拟数据测试逻辑应用而不影响生产环境的静态结果
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/13/2019
ms.openlocfilehash: b71aae91f4a065b70537a300aa0bd7016edfd4b4
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790280"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>通过设置静态结果，使用模拟数据测试逻辑应用

在测试逻辑应用时，出于各种原因，可能无法真正调用或访问应用、服务和系统。 通常在这些情况下，您可能必须运行不同的条件路径、强制错误、提供特定的消息响应正文，甚至尝试跳过一些步骤。 通过在逻辑应用中设置操作的静态结果，可以模拟该操作的输出数据。 对操作启用静态结果不会运行操作，而是返回模拟数据。

例如，如果为 Outlook 365 发送邮件操作设置了静态结果，则逻辑应用引擎只会返回指定为静态结果的模拟数据，而不是调用 Outlook 并发送电子邮件。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 要设置静态结果的逻辑应用

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>设置静态结果

1. 如果尚未这样做，请在[Azure 门户](https://portal.azure.com)中的逻辑应用设计器中打开逻辑应用。

1. 在要设置静态结果的操作上，执行以下步骤： 

   1. 在操作的右上角，选择省略号（ *"..."）按钮*，然后选择 "**静态结果**"，例如：

      ![选择 "静态结果" > "启用静态结果"](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. 选择 "**启用静态结果**"。 对于 "必需" （*）属性，请指定要为操作的响应返回的模拟输出值。

      例如，下面是 HTTP 操作的必需属性：

      | properties | 描述 |
      |----------|-------------|
      | **Status** | 要返回的操作的状态 |
      | **状态代码** | 要返回的特定状态代码 |
      | **标头** | 要返回的标头内容 |
      |||

      ![选择 "启用静态结果"](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      若要以 JavaScript 对象表示法（JSON）格式输入模拟数据，请选择 "**切换到 Json 模式**" （![选择 "切换到 json 模式"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)）。

   1. 对于可选属性，请打开 "**选择可选字段**" 列表，然后选择要模拟的属性。

      ![选择可选属性](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. 准备好保存后，选择 "**完成**"。

   在操作的右上角，标题栏现在显示测试烧杯图标（](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)的静态结果![图标），这表示已启用静态结果。

   ![显示已启用静态结果的图标](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   若要查找以前使用模拟数据的运行，请参阅本主题后面的 "[查找使用静态结果的运行](#find-runs-mock-data)"。

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>重新使用以前的输出

如果你的逻辑应用具有以前的运行方式，你可以将其用作模拟输出，你可以从该运行复制并粘贴输出。

1. 如果尚未这样做，请在[Azure 门户](https://portal.azure.com)中的逻辑应用设计器中打开逻辑应用。

1. 在逻辑应用的主菜单上，选择 "**概述**"。

1. 在 "**运行历史记录**" 部分中，选择要运行的逻辑应用。

1. 在逻辑应用的工作流中，找到并展开具有所需输出的操作。

1. 选择 "**显示原始输出**" 链接。

1. 复制完整的 JavaScript 对象表示法（JSON）对象或要使用的特定子节，例如，"输出" 部分，甚至只是标头部分。

1. 按照[设置静态结果](#set-up-static-results)中的操作的打开**静态结果**框中的步骤进行操作。

1. 打开 "**静态结果**" 框后，选择以下任一步骤：

   * 若要粘贴完整的 JSON 对象，请选择 "**切换到 Json 模式**" （![选择 "切换到 json 模式"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)）：

     ![对于 "完成" 对象，请选择 "切换到 JSON 模式"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * 若要仅粘贴 JSON 节，请在该节的标签旁边，为该部分选择 "**切换到 JSON 模式**"，例如：

     ![对于输出，请选择 "切换到 JSON 模式"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. 在 JSON 编辑器中，粘贴先前复制的 JSON。

   ![JSON 模式](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. 完成后，选择“完成”。 或者，若要返回设计器，请选择 "**切换编辑器模式**" （![选择 "切换编辑器模式"](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png)）。

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>查找使用静态结果的运行

逻辑应用的运行历史记录标识操作使用静态结果的运行。 若要查找这些运行，请执行以下步骤：

1. 在逻辑应用的主菜单上，选择 "**概述**"。 

1. 在右窗格中的 "**运行历史记录**" 下，找到 "**静态结果**" 列。 

   包含带有结果的操作的任何运行的 "**静态结果**" 列都设置为 "已**启用**"，例如：

   ![运行历史记录-静态结果列](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. 若要查看使用静态结果的操作，请选中 "要在其中将**静态结果**列设置为**启用**的运行"。

   使用静态结果的操作将显示 test 烧杯（![图标](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)）图标，例如：

   ![运行历史记录-使用静态结果的操作](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>禁用静态结果

关闭静态结果不会丢弃上次设置中的值。 因此，下次打开静态结果时，可以继续使用以前的值。

1. 查找要禁用静态输出的操作。 在操作的右上角，选择 "测试烧杯" 图标（![](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)的 "静态结果" 图标）。

   ![禁用静态结果](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. 选择 "**禁用静态结果**" > **完成**。

   ![禁用静态结果](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="reference"></a>参考

有关基础工作流定义中的此设置的详细信息，请参阅[静态结果-工作流定义语言](../logic-apps/logic-apps-workflow-definition-language.md#static-results)和 RuntimeConfiguration 的架构参考[。 StaticResult-运行时配置设置](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-configuration-settings)

## <a name="next-steps"></a>后续步骤

* 了解有关[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)的详细信息