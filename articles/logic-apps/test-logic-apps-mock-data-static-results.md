---
title: 使用模拟数据-Azure 逻辑应用的测试逻辑应用
description: 设置用于测试且不会影响生产环境的具有模拟数据的逻辑应用的静态结果
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 05/13/2019
ms.openlocfilehash: 45eeb20e5c572ddd98244b2e751322fcce1d4b76
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597192"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>通过设置静态结果测试具有模拟数据的逻辑应用

在测试逻辑应用时，不可能已准备好实际调用或由于各种原因而访问应用程序、 服务和系统。 通常在这些情况下，您可能需要运行不同条件路径、 强制错误、 提供特定的消息响应正文，或甚至可以试着跳过一些步骤。 通过设置静态结果在逻辑应用中的操作，可以模拟该操作的输出数据。 启用静态的结果是要执行的操作不会运行操作，但改为返回模拟数据。

例如，如果设置了静态结果适用于 Outlook 365 发送邮件操作，在逻辑应用引擎只返回模拟数据指定为静态的结果，而不是调用 Outlook 和发送电子邮件。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 想要设置静态结果的逻辑应用

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>设置静态结果

1. 如果你尚未准备好，在[Azure 门户](https://portal.azure.com)，在逻辑应用设计器中打开逻辑应用。

1. 在你想要设置静态结果的操作，请执行以下步骤： 

   1. 在操作的右上角，选择省略号 (*...*) 按钮，然后选择**静态结果**，例如：

      ![选择"静态结果">"启用静态结果"](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. 选择**启用静态结果**。 对于需要 （*） 属性，指定想要返回的操作响应的模拟输出值。

      例如，下面是 HTTP 操作的必需的属性：

      | 属性 | 描述 |
      |----------|-------------|
      | **Status** | 若要返回的操作的状态 |
      | **状态代码** | 要返回的特定状态代码 |
      | **标头** | 要返回的标头内容 |
      |||

      ![选择"启用静态结果"](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      若要以 JavaScript 对象表示法 (JSON) 格式输入模拟数据，请选择**切换到 JSON 模式**(![选择"切换到 JSON 模式"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png))。

   1. 对于可选属性，打开**选择可选字段**列表，然后选择你想要模拟的属性。

      ![选择可选属性](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. 如果你已准备好保存，选择**完成**。

   在操作的右上角的标题栏现在显示测试烧杯图标 (![静态结果图标](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png))，指示已启用静态结果。

   ![图标显示启用了静态结果](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   若要查找使用模拟数据的之前运行，请参阅[查找使用静态结果运行](#find-runs-mock-data)本主题中更高版本。

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>重复使用以前的输出

如果逻辑应用具有上一次运行具有输出可以重用为模拟的输出，您可以复制并粘贴从运行的输出。

1. 如果你尚未准备好，在[Azure 门户](https://portal.azure.com)，在逻辑应用设计器中打开逻辑应用。

1. 在逻辑应用的主菜单上，选择**概述**。

1. 在中**运行历史记录**部分中，选择逻辑应用运行你想。

1. 在逻辑应用的工作流中找到并展开具有所需的输出的操作。

1. 选择**显示原始输出**链接。

1. 复制完整的 JavaScript 对象表示法 (JSON) 对象或你想要使用，例如特定子部分、 outputs 节中或甚至只是标头部分。

1. 按照这些步骤来打开**静态结果**框中为你操作[设置静态结果](#set-up-static-results)。

1. 之后**静态结果**将打开，选择任一步骤：

   * 若要粘贴完整的 JSON 对象，请选择**切换到 JSON 模式**(![选择"切换到 JSON 模式"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)):

     ![为完整的对象选择"切换到 JSON 模式"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * 若要粘贴只是一个 JSON 部分中的，该部分标签旁选择**切换到 JSON 模式**的该部分中，例如：

     ![为输出中选择"切换到 JSON 模式"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. 在 JSON 编辑器中，粘贴先前复制的 JSON。

   ![JSON 模式](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. 完成后，选择“完成”。 或者，若要返回到设计器，选择**交换机编辑器模式**(![选择"切换编辑器模式"](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png))。

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>查找使用静态结果的运行

在逻辑应用的运行历史记录标识操作在其中使用静态结果的运行。 若要查找这些运行，请执行以下步骤：

1. 在逻辑应用的主菜单上，选择**概述**。 

1. 在右窗格中下,**运行历史记录**，找到**静态结果**列。 

   包含操作结果与任何运行都有**静态结果**列设置为**已启用**，例如：

   ![运行历史记录-静态结果列](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. 若要查看使用静态结果的操作，请选择所需的位置运行**静态结果**列设置为**已启用**。

   对于使用静态结果的操作显示测试烧杯 (![静态结果图标](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)) 图标，例如：

   ![运行历史记录-使用静态结果的操作](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>禁用静态结果

关闭静态结果不会丢弃值从最后一个安装。 因此，当您打开静态结果下一次，你可以继续使用以前的值。

1. 查找想要禁用静态输出操作。 在操作的右上角，选择测试烧杯图标 (![静态结果图标](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png))。

   ![禁用静态结果](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. 选择**禁用静态结果** > **完成**。

   ![禁用静态结果](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="reference"></a>参考

有关在基础的工作流定义中此设置的详细信息，请参阅[静态结果-的工作流定义语言架构参考](../logic-apps/logic-apps-workflow-definition-language.md#static-results)和[runtimeConfiguration.staticResult-运行时配置设置](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-configuration-settings)

## <a name="next-steps"></a>后续步骤

* 详细了解[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)