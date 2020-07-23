---
title: 连接到 Oracle 数据库
description: 使用 Oracle Database REST API 和 Azure 逻辑应用插入和管理记录
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/20/2020
tags: connectors
ms.openlocfilehash: e7055964f9515189450fac5c993f6acda946bfd4
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86524159"
---
# <a name="get-started-with-the-oracle-database-connector"></a>Oracle 数据库连接器入门

使用 Oracle 数据库连接器，可以创建使用现有数据库中数据的组织工作流。 此连接器可以连接到本地 Oracle 数据库，或者连接到安装了 Oracle 数据库的 Azure 虚拟机。 借助此连接器，可以：

* 通过向客户数据库添加新客户或在订单数据库中更新订单生成工作流。
* 使用操作获取数据行、插入新行，甚至删除。 例如，在 Dynamics CRM Online 中创建记录时（触发器），则在 Oracle 数据库中插入行（操作）。 

该连接器不支持以下项：

* 视图 
* 包含复合键的任意表
* 表中的嵌套对象类型
* 具有非标量值的数据库函数

本文介绍如何在逻辑应用中使用 Oracle 数据库连接器。

## <a name="prerequisites"></a>先决条件

* 支持的 Oracle 版本： 
    * Oracle 9 及更高版本
    * Oracle 客户端软件 8.1.7 及更高版本

* 安装本地数据网关。 [从逻辑应用连接到本地数据](../logic-apps/logic-apps-gateway-connection.md)一文列出了相关步骤。 若要连接到 Oracle 数据库，或者连接到安装了 Oracle DB 的 Azure VM，网关是必需的。 

    > [!NOTE]
    > 本地数据网关的作用好似一架桥，提供本地数据（不在云中的数据）与逻辑应用之间的安全数据传输。 可以将同一网关用于多个服务和多个数据源。 因此，可能只需安装网关一次。

* 将 Oracle 客户端与本地数据网关安装在同一计算机上。 请务必安装 Oracle 提供的 64 位用于 .Net 的 Oracle 数据提供程序：  

  [用于 Windows x64 的 64 位 ODAC 12c Release 4 (12.1.0.2.4)](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > 如果未安装 Oracle 客户端，则在尝试创建或使用连接时，会发生错误。 请参阅本文中的常见错误。


## <a name="add-the-connector"></a>添加连接器

> [!IMPORTANT]
> 此连接器没有任何触发器。 它只有操作。 因此，请在创建逻辑应用时，添加另一个用于启动逻辑应用的触发器，例如“计划 - 重复周期”或“请求/响应 - 响应”。  

1. 在 [Azure 门户](https://portal.azure.com)中，创建一个空白逻辑应用。

2. 在启动逻辑应用时，请选择“请求/响应 - 请求”触发器： 

    ![对话框中有一个用于搜索所有触发器的框。 还有一个触发器，其中显示了 "请求/响应请求"，其中包含选择按钮。](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. 选择“保存”。 保存时，会自动生成请求 URL。 

4. 选择“新步骤”，并选择“添加操作”。 若要查看可用操作，请键入 `oracle`： 

    ![搜索框中包含 "oracle"。 搜索会生成一个名为 "Oracle Database" 的命中。 有一个选项卡式页面，一个选项卡显示 "触发器（0）"，另一个选项卡显示 "操作（6）"。 列出了六个操作。 首先是 "获取行预览"。](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > 这也是查看适用于任何连接器的触发器和操作的最快方式。 键入连接器的部分名称，例如 `oracle`。 设计器会列出任何触发器和任何操作。 

5. 选择其中一个操作，例如“Oracle 数据库 - 获取行”。 选择“通过本地数据网关连接”。 输入 Oracle 服务器名称、身份验证方法、用户名、密码，并选择网关：

    ![该对话框的标题为 "Oracle Database 获取行"。 已选中一个框，其中标记为 "通过本地数据网关连接"。 下面是五个其他文本框。](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. 连接后，从列表中选择一个表，并向表中输入行 ID。 需要知道表的标识符。 如果不知道该标识符，请与 Oracle DB 管理员联系，从 `select * from yourTableName` 获取输出。 这样即可获取进一步操作所需的标识信息。

    在以下示例中，从“人力资源”数据库返回了作业数据： 

    ![标题为 "获取行（预览）" 的对话框具有两个文本框： "表名称"，其中包含 "HRJOBS" 且包含下拉列表和 "行 id"，其中包含 "SA_REP"。](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. 在这个后续步骤中，可以使用任何其他连接器来生成工作流。 如果要测试从 Oracle 获取数据，请使用一个发送电子邮件连接器（如 Office 365 Outlook）向自己发送一封包含 Oracle 数据的电子邮件。 使用 Oracle 表中的动态令牌生成电子邮件的 `Subject` 和 `Body`：

    ![有两个对话框。 "发送电子邮件框" 包含用于指定电子邮件的 "正文"、"主题" 和 "目标" 地址的框。 "添加动态内容" 对话框提供了来自流的应用和服务的动态内容搜索。](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **保存**逻辑应用，并选择“运行”。 关闭设计器，并在运行历史记录中查看状态。 如果该操作失败，请选择失败的消息行。 设计器会打开，并且会显示具体的失败步骤，以及错误信息。 如果该操作成功，应该会收到一封电子邮件，其中包含你添加的信息。


### <a name="workflow-ideas"></a>工作流创意

* 想要监视 #oracle 哈希标记，并将推文置于数据库中，使之可以在其他应用程序中查询和使用。 在逻辑应用中添加`Twitter - When a new tweet is posted`触发器，并输入 **#oracle** 哈希标记。 然后添加`Oracle Database - Insert row`操作并选择表：

    !["当发布新的推文时" 对话框显示 "# 井号 oracle" 作为搜索文本，并允许您指定检查频率。 此对话框将引导到 "Oracle Database" 对话框，通过该对话框可以选择操作。](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* 此时会向服务总线队列发送消息。 想要获取这些消息并将其置于数据库中。 在逻辑应用中，添加`Service Bus - when a message is received in a queue`触发器，并选择队列。 然后添加`Oracle Database - Insert row`操作并选择表：

    !["收到消息时收到 ..."对话框将 "订单" 显示为 "队列名称"，并允许您指定检查频率。 此框将导致 "插入行（预览）" 对话框，使您可以选择 "表名称"。](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>常见错误

#### <a name="error-cannot-reach-the-gateway"></a>**错误**：无法访问网关

**原因：** 本地数据网关不能连接到云。 

**缓解措施**：请确保网关正在安装了该网关的本地计算机上运行，并确保该网关可以连接到 Internet。  建议不要将网关安装在可能会关闭或进入睡眠状态的计算机上。 还可以重新启动本地数据网关服务 (PBIEgwService)。

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-see-httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**错误**：所使用的提供程序已弃用：“System.Data.OracleClient 需要 Oracle 客户端软件 8.1.7 版或更高版本”。 请参阅 [https://go.microsoft.com/fwlink/p/?LinkID=272376](https://go.microsoft.com/fwlink/p/?LinkID=272376) 安装正式的提供程序。

**原因：** Oracle 客户端 SDK 未安装在运行本地数据网关的计算机上。  

**解决方法**：下载 Oracle 客户端 SDK 并将其与本地数据网关安装在同一计算机上。

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**错误**：表“[表名]”未定义任何键列

**原因：** 该表没有主键。  

**解决方法**：Oracle Database 连接器要求使用包含主键列的表。
 
## <a name="connector-specific-details"></a>特定于连接器的详细信息

在[连接器详细信息](/connectors/oracle/)中查看在 Swagger 中定义的触发器和操作，并查看限制。 

## <a name="get-some-help"></a>获取帮助

若要提问、解答问题和了解其他逻辑应用用户的活动，请访问[有关 Azure 逻辑应用的 Microsoft 问答页面](https://docs.microsoft.com/answers/topics/azure-logic-apps.html)。 

可以在 [https://aka.ms/logicapps-wish](https://aka.ms/logicapps-wish) 上投票并提交自己的创意，帮助改进逻辑应用和连接器。 


## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)，并在 [API 列表](apis-list.md)中了解逻辑应用中的可用连接器。
