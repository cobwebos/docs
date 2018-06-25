---
title: 连接到 SAP 系统 - Azure 逻辑应用 | Microsoft Docs
description: 如何使用 Azure 逻辑应用将工作流自动化，以访问和管理 SAP 资源
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/31/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, divswa, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: a9346092e0a24709a9888937effdf802bf1b09fb
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300209"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>从 Azure 逻辑应用连接到 SAP 系统

本文介绍如何使用 SAP 应用程序服务器和 SAP 消息服务器连接器，从逻辑应用内部访问 SAP 资源。 这样，便可以通过创建逻辑应用来自动执行任务、过程和工作流，以管理 SAP 数据和资源。

本示例使用可通过 HTTP 请求触发的逻辑应用。 该逻辑应用将一个中间文档 (IDoc) 发送到 SAP 服务器，并向调用逻辑应用的请求方返回响应。
当前的 SAP 连接器包含操作，但不包含触发器，因此，本示例使用 [HTTP 请求触发器](../connectors/connectors-native-reqres.md)作为逻辑应用工作流中的第一个步骤。 有关特定于 SAP 连接器的技术信息，请参阅以下参考文章： 

* <a href="https://docs.microsoft.com/connectors/sapapplicationserver/" target="blank">SAP 应用程序服务器连接器</a>
* <a href="https://docs.microsoft.com/connectors/sapmessageserver/" target="blank">SAP 消息服务器连接器</a>

如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。

## <a name="prerequisites"></a>先决条件

若要遵循本文中的步骤，需准备好以下各项：

* 要从中访问 SAP 系统的逻辑应用，以及用于启动逻辑应用工作流的触发器。 SAP 连接器目前仅提供操作。 如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* <a href="https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server" target="_blank">SAP 应用程序服务器</a>或 <a href="https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm" target="_blank">SAP 消息服务器</a>

* 在任何本地计算机上下载并安装最新的[本地数据网关](https://www.microsoft.com/download/details.aspx?id=53127)。 在继续操作之前，请确保在 Azure 门户中设置网关。 网关有助于安全访问数据，资源位于本地。 有关详细信息，请参阅[为 Azure 逻辑应用安装本地数据网关](../logic-apps/logic-apps-gateway-install.md)。

* 在本地数据网关所安装到的同一台计算机上，下载并安装最新的 SAP 客户端库，目前为<a href="https://softwaredownloads.sap.com/file/0020000000086282018" target="_blank">适用于 Microsoft .NET Framework 4.0 和 Windows 64 位 (x64) 的 SAP 连接器 (NCo) 3.0.20.0</a>。 安装此版本或更高版本的原因是：

  * 如果同时发送多个 IDoc 消息，早期的 SAP NCo 版本可能死锁。 
  这种状态会阻止向 SAP 目标发送所有后续消息，从而导致消息超时。

  * 本地数据网关只能在 64 位系统上运行。 
  否则，会收到“错误的映像”错误，因为数据网关主机服务不支持 32 位程序集。

  * 数据网关主机服务和 Microsoft SAP 适配器使用 .NET Framework 4.5。 SAP NCo for .NET Framework 4.0 适用于使用 .NET 运行时 4.0 到 4.7.1 的进程。 
  SAP NCo for .NET Framework 2.0 适用于使用 .NET 运行时 2.0 到 3.5 的进程，而不再适用于最新的本地数据网关。

* 可发送到 SAP 服务器的消息内容，例如示例 IDoc 文件。 此内容必须采用 XML 格式，并且包含要使用的 SAP 操作的命名空间。

<a name="add-trigger"></a>

## <a name="add-http-request-trigger"></a>添加 HTTP 请求触发器

在 Azure 逻辑应用中，每个逻辑应用都必须从[触发器](../logic-apps/logic-apps-overview.md#logic-app-concepts)开始，该触发器在发生特定事件或特定条件得到满足的情况下触发。 每当触发器触发时，逻辑应用引擎就会创建一个逻辑应用实例并开始运行应用的工作流。

本示例创建一个包含 Azure 中的终结点的逻辑应用，以便可将 *HTTP POST 请求*发送到逻辑应用。 当逻辑应用程序收到这些 HTTP 请求时，触发器将会触发，并运行工作流中的下一个步骤。

1. 在 Azure 门户中创建一个空白的逻辑应用，以便打开逻辑应用设计器。 

2. 在搜索框中，输入“http 请求”作为筛选器。 在触发器列表中选择此触发器：“请求 - 当收到 HTTP 请求时”

   ![添加 HTTP 请求触发器](./media/logic-apps-using-sap-connector/add-trigger.png)

3. 现在请保存逻辑应用，以便可为逻辑应用生成终结点 URL。
在设计器工具栏上，选择“保存”。 

   终结点 URL 现在会显示在触发器中，例如：

   ![生成终结点的 URL](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

## <a name="add-sap-action"></a>添加 SAP 操作

在 Azure 逻辑应用中，[操作](../logic-apps/logic-apps-overview.md#logic-app-concepts)是指工作流中紧跟在某个触发器或另一操作后面执行的一个步骤。 如果尚未将触发器添加到逻辑应用并想要遵循本示例，请[添加此部分所述的触发器](#add-trigger)。

1. 在逻辑应用设计器的触发器下，选择“新建步骤” > “添加操作”。

   ![添加操作](./media/logic-apps-using-sap-connector/add-action.png) 

2. 在搜索框中，输入“sap 服务器”作为筛选器。 在操作列表中选择 SAP 服务器的操作： 

   * **SAP 应用程序服务器 - 发送到 SAP**
   * **SAP 消息服务器 - 发送到 SAP**

   本示例使用此操作：“SAP 应用程序服务器 - 发送到 SAP”

   ![选择“SAP 应用程序服务器”或“SAP 消息服务器”](media/logic-apps-using-sap-connector/select-sap-action.png)

3. 如果系统提示输入连接详细信息，请立即创建 SAP 连接。 如果连接已存在，请继续下一步，以便可以设置 SAP 操作。 

   **创建本地 SAP 连接**

   1. 对于“网关”，请选择“通过本地数据网关连接”，以显示本地连接属性。

   2. 提供 SAP 服务器的连接信息。 
   对于“网关”属性，请选择在 Azure 门户中为网关安装创建的数据网关，例如：

      **SAP 应用程序服务器**

      ![创建 SAP 应用程序服务器连接](./media/logic-apps-using-sap-connector/create-SAP-app-server-connection.png)  

      **SAP 消息服务器**

      ![创建 SAP 消息服务器连接](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png) 

   2. 完成后，选择“创建”。

      逻辑应用会设置并测试连接，确保连接正常工作。

4. 现在，请找到并选择 SAP 服务器中的某个操作。 

   1. 在“SAP 操作”框中，选择文件夹图标。 
   在文件夹列表中，找到并选择要使用的操作。 

      本示例选择了 IDoc 操作的 **IDOC** 类别。 

      ![找到并选择 IDoc 操作](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      如果找不到所需的操作，可以手动输入路径，例如：

      ![手动提供 IDoc 操作的路径](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      有关 IDoc 操作的详细信息，请参阅 [IDOC 操作的消息架构](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

   2. 在“输入消息”框中单击，以显示动态内容列表。 
   在该列表中的“当收到 HTTP 请求时”下面，选择“正文”字段。 

      此步骤包含来自 HTTP 请求触发器的正文内容，并将该输出发送到 SAP 服务器。

      ![选择“正文”字段](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      完成后，SAP 操作如以下示例所示：

      ![完成 SAP 操作](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

6. 保存逻辑应用。 在设计器工具栏上，选择“保存”。

<a name="add-response"></a>

## <a name="add-http-response-action"></a>添加 HTTP 响应操作

现在，请将响应操作添加到逻辑应用的工作流，并包含来自 SAP 操作的输出。 这样，逻辑应用便可将来自 SAP 服务器的结果返回到原始请求方。 

1. 在逻辑应用设计器中的 SAP 操作下，选择“新建步骤” > “添加操作”。

2. 在搜索框中，输入“响应”作为筛选器。 在操作列表中选择此操作：“请求 - 响应”

3. 在“正文”框中单击，以显示动态内容列表。 在该列表中的“发送到 SAP”下面，选择“正文”字段。 

   ![完成 SAP 操作](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

4. 保存逻辑应用。 

## <a name="test-your-logic-app"></a>测试逻辑应用

1. 如果尚未启用逻辑应用，请在逻辑应用菜单中选择“概述”。 在工具栏上选择“启用”。 

2. 在逻辑应用设计器工具栏上，选择“运行”。 此步骤会手动启动逻辑应用。

3. 通过将 HTTP POST 请求发送到 HTTP 请求触发器中的 URL 来触发逻辑应用，并在请求中包含消息内容。 若要发送请求，可以使用 [Postman](https://www.getpostman.com/apps) 等工具。 

   在本文中，请求会发送 IDoc 文件，该文件必须采用 XML 格式，并且包含所用 SAP 操作的命名空间，例如： 

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//620/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

4. 发送 HTTP 请求之后，等待逻辑应用返回响应。

> [!NOTE]
> 如果未在[请求超时限制](./logic-apps-limits-and-config.md)所规定的时间内完成响应所需的所有步骤，逻辑应用可能会超时。 如果发生这种情况，请求可能会被阻止。 为帮助诊断问题，请了解如何[检查和监视逻辑应用](../logic-apps/logic-apps-monitor-your-logic-apps.md)。

祝贺你，现已创建一个可与 SAP 服务器通信的逻辑应用。 为逻辑应用设置 SAP 连接后，可以探索其他可用的 SAP 操作，例如 BAPI 和 RFC。

## <a name="connector-reference"></a>连接器参考

有关连接器 Swagger 文件描述的连接器技术详细信息，请参阅以下参考文章： 

* [SAP 应用程序服务器](/connectors/sapapplicationserver/)
* [SAP 消息服务器](/connectors/sapmessageserver/)

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](http://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* 从逻辑应用[连接到本地系统](../logic-apps/logic-apps-gateway-connection.md)
* 了解如何使用 [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) 执行验证、转换和其他消息操作。
* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
