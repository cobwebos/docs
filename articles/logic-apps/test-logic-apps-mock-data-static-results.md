---
title: 使用模拟数据测试逻辑应用 - Azure 逻辑应用
description: 设置静态结果，以在不影响生产环境的情况下使用模拟数据测试逻辑应用
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 05/13/2019
ms.openlocfilehash: 45eeb20e5c572ddd98244b2e751322fcce1d4b76
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65597192"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>通过设置静态结果来使用模拟数据测试逻辑应用

测试逻辑应用时，可能出于各种原因而尚未准备好实际调用或访问应用、服务与系统。 通常，在这些情况下，可能需要运行不同的条件路径、强制错误、提供特定的消息响应正文，甚至尝试跳过某些步骤。 针对逻辑应用中的某个操作设置静态结果，可以模拟该操作的输出数据。 针对某个操作启用静态结果不会运行该操作，而是返回模拟数据。

例如，如果你针对 Outlook 365 中的发送邮件操作设置了静态结果，则逻辑应用引擎只会返回指定为静态结果的模拟数据，而不会调用 Outlook 并发送电子邮件。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 要在其中设置静态结果的逻辑应用

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>设置静态结果

1. 在 [Azure 门户](https://portal.azure.com)中，在逻辑应用设计器中打开你的逻辑应用（如果尚未打开）。

1. 针对要在其中设置静态结果的操作执行以下步骤： 

   1. 在该操作的右上角选择省略号 ( *...* ) 按钮，然后选择“静态结果”，例如： 

      ![选择“静态结果”>“启用静态结果”](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. 选择“启用静态结果”。  对于必需的 (*) 属性，请指定要为操作响应返回的模拟输出值。

      例如，下面是 HTTP 操作的必需属性：

      | 属性 | 描述 |
      |----------|-------------|
      | **Status** | 要返回的操作状态 |
      | **状态代码** | 要返回的特定状态代码 |
      | **标头** | 要返回的标头内容 |
      |||

      ![选择“启用静态结果”](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      若要以 JavaScript 对象表示法 (JSON) 格式输入模拟数据，请选择“切换到 JSON 模式”（![选择“切换到 JSON 模式”](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)）。 

   1. 对于可选属性，请打开“选择可选字段”列表，然后选择要模拟的属性。 

      ![选择可选属性](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. 准备好保存时，选择“完成”。 

   在该操作的右上角，标题栏现在会显示量杯图标（![静态结果的图标](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)），表示已启用静态结果。

   ![显示已启用静态结果的图标](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   若要查找使用模拟数据的先前运行，请参阅本主题稍后的[查找使用静态结果的运行](#find-runs-mock-data)。

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>重复使用先前的输出

如果逻辑应用中的某个先前运行包含可重用为模拟输出的输出，则你可以从该运行复制并粘贴这些输出。

1. 在 [Azure 门户](https://portal.azure.com)中，在逻辑应用设计器中打开你的逻辑应用（如果尚未打开）。

1. 在逻辑应用的主菜单中选择“概述”  。

1. 在“运行历史记录”部分，选择所需的逻辑应用运行。 

1. 在逻辑应用的工作流中，找到并展开包含所需输出的操作。

1. 选择“显示原始输出”链接。 

1. 复制所需的完整 JavaScript 对象表示法 (JSON) 对象或特定的子节，例如 outputs 节，或者只是 headers 节。

1. 遵循[设置静态结果](#set-up-static-results)中的步骤打开操作的“静态结果”框。 

1. “静态结果”框打开后，选择以下任一步骤： 

   * 若要粘贴完整的 JSON 对象，请选择“切换到 JSON 模式”（![选择“切换到 JSON 模式”](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)）： 

     ![为完整对象选择“切换到 JSON 模式”](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * 若只要粘贴某个 JSON 节，请在该节的标签旁边，选择该节对应的“切换到 JSON 模式”，例如： 

     ![为输出选择“切换到 JSON 模式”](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. 在 JSON 编辑器中，粘贴前面复制的 JSON。

   ![JSON 模式](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. 完成后，选择“完成”  。 或者，若要返回设计器，请选择“切换编辑器模式”（![选择“切换编辑器模式”](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png)）。 

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>查找使用静态结果的运行

在逻辑应用的运行历史记录中，标识其中的操作使用静态结果的运行。 若要查找这些运行，请执行以下步骤：

1. 在逻辑应用的主菜单中选择“概述”  。 

1. 在右窗格中的“运行历史记录”下，找到“静态结果”列。   

   包含操作和结果的任何运行的“静态结果”列已设置为“已启用”，例如：  

   ![运行历史记录 -“静态结果”列](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. 若要查看使用静态结果的操作，请选择“静态结果”列设置为“已启用”的所需运行。  

   使用静态结果的操作会显示量杯图标（![静态结果的图标](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)），例如：

   ![运行历史记录 - 使用静态结果的操作](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>禁用静态结果

禁用静态结果不会丢弃最近设置中的值。 因此，下一次启用静态结果时，可以继续使用以前的值。

1. 查找要在其中禁用静态输出的操作。 在该操作的右上角，选择量杯图标（![静态结果的图标](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)）。

   ![禁用静态结果](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. 选择“禁用静态结果” > “完成”。  

   ![禁用静态结果](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="reference"></a>参考

有关基础工作流定义中的此项设置的详细信息，请参阅[静态结果 - 工作流定义语言的架构参考](../logic-apps/logic-apps-workflow-definition-language.md#static-results)和 [runtimeConfiguration.staticResult - 运行时配置设置](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-configuration-settings)

## <a name="next-steps"></a>后续步骤

* 详细了解 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)