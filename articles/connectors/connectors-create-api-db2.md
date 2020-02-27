---
title: 访问和管理 IBM DB2 资源
description: 使用 Azure 逻辑应用生成自动化工作流，读取、编辑、更新和管理 IBM DB2 资源
services: logic-apps
ms.suite: integration
ms.reviewer: plarsen, logicappspm
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: 32b482607827ee4420e39b1936586d64f9ea3139
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77651375"
---
# <a name="access-and-manage-ibm-db2-resources-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用访问和管理 IBM DB2 资源

通过[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[IBM DB2 连接器](/connectors/db2/)，你可以根据存储在 DB2 数据库中的资源创建自动化任务和工作流。 工作流可以连接到数据库中的资源、读取和列出数据库表、添加行、更改行、删除行，以及执行其他操作。 可在逻辑应用中包含操作，用于从数据库获取响应，并使输出可供其他操作使用。

本文介绍如何创建一个可执行各种数据库操作的逻辑应用。 如果你不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

## <a name="supported-platforms-and-versions"></a>支持的平台和版本

DB2 连接器包含一个可以通过 TCP/IP 网络来与远程 DB2 服务器通信的 Microsoft 客户端。 此连接器可用于访问云数据库，如在 Azure 虚拟化中运行的用于 Windows 的 IBM DB2。 此外，可在[安装并设置本地数据网关](../logic-apps/logic-apps-gateway-connection.md)之后访问本地 DB2 数据库。

IBM DB2 连接器支持以下 IBM DB2 平台和版本，以及支持分布式关系数据库体系结构（DRDA） SQL 访问管理器（SQLAM）版本10和11的 IBM DB2 兼容产品：

| 平台 | 版本 | 
|----------|---------|
| IBM DB2 for z/OS | 11.1、10.1 |
| IBM DB2 for i | 7.3、7.2、7.1 |
| IBM DB2 for LUW | 11、10.5 |
|||

## <a name="supported-database-operations"></a>支持的数据库操作

IBM DB2 连接器支持以下数据库操作，这些操作映射到连接器中的相应操作：

| 数据库操作 | 连接器操作 |
|--------------------|------------------|
| 列出数据库表 | 获取表 |
| 使用 SELECT 读取一行 | 获取行 |
| 使用 SELECT 读取全部行 | 获取行 |
| 使用 INSERT 添加一行 | 插入行 |
| 使用 UPDATE 编辑一行 | 更新行 |
| 使用 DELETE 删除一行 | 删除行 |
|||

## <a name="prerequisites"></a>必备条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 基于云或本地的 IBM DB2 数据库

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 要在其中访问 DB2 数据库的逻辑应用。 此连接器仅提供操作，因此，若要启动逻辑应用，请选择单独的触发器（例如“重复”触发器）。
本文中的示例使用“重复”触发器。

<a name="add-db2-action"></a>

## <a name="add-db2-action---get-tables"></a>添加 DB2 操作 - 获取表

1. 在 [Azure 门户](https://portal.azure.com)中，在逻辑应用设计器中打开你的逻辑应用（如果尚未打开）。

1. 在触发器下，选择“新建步骤”。

1. 在搜索框中，输入“db2”作为筛选器。 对于本示例，请在操作列表中选择以下操作：“获取表(预览)”

   ![选择操作](./media/connectors-create-api-db2/select-db2-action.png)

   此时，系统会提示你提供 DB2 数据库的连接详细信息。

1. 请遵循为[云数据库](#cloud-connection)或[本地数据库](#on-premises-connection)创建连接的步骤。

<a name="cloud-connection"></a>

## <a name="connect-to-cloud-db2"></a>连接到云 DB2

若要设置连接，请按提示提供以下连接详细信息，选择“创建”，然后保存逻辑应用：

| properties | 必选 | 说明 |
|----------|----------|-------------|
| **通过本地网关连接** | 否 | 仅适用于本地连接。 |
| **连接名称** | 是 | 连接的名称，例如“MyLogicApp-DB2-connection” |
| **Server** | 是 | DB2 服务器的地址或冒号分隔的别名端口号，例如“myDB2server.cloudapp.net:50000” <p><p>**注意**：此值是表示 TCP/IP 地址或别名的字符串，采用 IPv4 或 IPv6 格式，后接冒号和 TCP/IP 端口号。 |
| **Database** | 是 | 数据库的名称 <p><p>**注意**：此值是表示 DRDA 关系数据库名称 (RDBNAM) 的字符串。 <p>- DB2 for z/OS 接受 16 字节字符串，其中的数据库称为“IBM DB2 for z/OS”位置。 <br>- DB2 for i 接受 18 字节字符串，其中的数据库称为“IBM DB2 for i”关系数据库。 <br>- DB2 for LUW 接受 8 字节字符串。 |
| **用户名** | 是 | 数据库的用户名 <p><p>**注意**：此值是一个字符串，其长度基于特定的数据库： <p><p>- DB2 for z/OS 接受 8 字节字符串。 <br>- DB2 for i 接受 10 字节字符串。 <br>- DB2 for Linux/UNIX 接受 8 字节字符串。 <br>- DB2 for Windows 接受 30 字节字符串。 |
| **密码** | 是 | 数据库的密码 |
||||

例如：

![基于云的数据库的连接详细信息](./media/connectors-create-api-db2/create-db2-cloud-connection.png)

<a name="on-premises-connection"></a>

## <a name="connect-to-on-premises-db2"></a>连接到本地 DB2

在创建连接之前，必须已安装本地数据网关。 否则无法完成连接设置。 如果已安装网关，请继续提供这些连接详细信息，然后选择“创建”。

| properties | 必选 | 说明 |
|----------|----------|-------------|
| **通过本地网关连接** | 是 | 适用于创建本地连接，将显示本地连接属性。 |
| **连接名称** | 是 | 连接的名称，例如“MyLogicApp-DB2-connection” | 
| **Server** | 是 | DB2 服务器的地址或冒号分隔的别名端口号，例如“myDB2server:50000” <p><p>**注意**：此值是表示 TCP/IP 地址或别名的字符串，采用 IPv4 或 IPv6 格式，后接冒号和 TCP/IP 端口号。 |
| **Database** | 是 | 数据库的名称 <p><p>**注意**：此值是表示 DRDA 关系数据库名称 (RDBNAM) 的字符串。 <p>- DB2 for z/OS 接受 16 字节字符串，其中的数据库称为“IBM DB2 for z/OS”位置。 <br>- DB2 for i 接受 18 字节字符串，其中的数据库称为“IBM DB2 for i”关系数据库。 <br>- DB2 for LUW 接受 8 字节字符串。 |
| **身份验证** | 是 | 连接的身份验证类型，例如“Basic” <p><p>**注意**：从列表中选择此值，包括“Basic”或“Windows (Kerberos)”。 |
| **用户名** | 是 | 数据库的用户名 <p><p>**注意**：此值是一个字符串，其长度基于特定的数据库： <p><p>- DB2 for z/OS 接受 8 字节字符串。 <br>- DB2 for i 接受 10 字节字符串。 <br>- DB2 for Linux/UNIX 接受 8 字节字符串。 <br>- DB2 for Windows 接受 30 字节字符串。 |
| **密码** | 是 | 数据库的密码 |
| **网关** | 是 | 安装的本地数据网关的名称 <p><p>**注意**：从列表中选择此值，包括 Azure 订阅和资源组中所有已安装的数据网关。 |
||||

例如：

![本地数据库的连接详细信息](./media/connectors-create-api-db2/create-db2-on-premises-connection.png)

### <a name="view-output-tables"></a>查看输出表

若要手动运行逻辑应用，请在设计器工具栏上选择“运行”。 完成运行逻辑应用后，可以查看运行后的输出。

1. 在逻辑应用菜单中，选择“概述”。

1. 在“运行历史记录”部分中的“摘要”下，选择最近的运行，即列表中的第一项。

   ![查看运行历史记录](./media/connectors-create-api-db2/run-history.png)

1. 在“逻辑应用运行”下，现在可以查看逻辑应用中每个步骤的状态、输入和输出。
展开“获取表”操作。

   ![展开操作](./media/connectors-create-api-db2/expand-action-step.png)

1. 若要查看输入，请选择“显示原始输入”。

1. 若要查看输出，请选择“显示原始输出”。

   输出包括一个表列表。

   ![查看输出表](./media/connectors-create-api-db2/db2-connector-get-tables-outputs.png)

## <a name="get-row"></a>获取行

若要提取 DB2 数据库表中的一条记录，请在逻辑应用中使用“获取一行”操作。 此操作运行 DB2 `SELECT WHERE` 语句，例如 `SELECT FROM AREA WHERE AREAID = '99999'`。

1. 如果以前从未在逻辑应用中用过 DB2 操作，请查看[添加 DB2 操作 - 获取表](#add-db2-action)部分中的步骤，但应该添加“获取一行”操作，然后返回此处以继续。

   添加“获取一行”操作后，示例逻辑应用如下所示：

   ![“获取一行”操作](./media/connectors-create-api-db2/db2-get-row-action.png)

1. 指定所有必需属性 (*) 的值。 选择一个表后，该操作会显示特定于该表中的记录的相关属性。

   | properties | 必选 | 说明 |
   |----------|----------|-------------|
   | **表名称** | 是 | 包含所需记录的表，在本示例中为“AREA” |
   | **地区 ID** | 是 | 所需记录的 ID，在本示例中为“99999” |
   ||||

   ![选择表](./media/connectors-create-api-db2/db2-get-row-action-select-table.png)

1. 完成后，请在设计器工具栏上选择“保存”。

### <a name="view-output-row"></a>查看输出行

若要手动运行逻辑应用，请在设计器工具栏上选择“运行”。 完成运行逻辑应用后，可以查看运行后的输出。

1. 在逻辑应用菜单中，选择“概述”。

1. 在“运行历史记录”部分中的“摘要”下，选择最近的运行，即列表中的第一项。

1. 在“逻辑应用运行”下，现在可以查看逻辑应用中每个步骤的状态、输入和输出。
展开“获取一行”操作。

1. 若要查看输入，请选择“显示原始输入”。

1. 若要查看输出，请选择“显示原始输出”。

   输出包括指定的行。

   ![查看输出行](./media/connectors-create-api-db2/db2-connector-get-row-outputs.png)

## <a name="get-rows"></a>获取行

若要提取 DB2 数据库表中的所有记录，请在逻辑应用中使用“获取多行”操作。 此操作运行 DB2 `SELECT` 语句，例如 `SELECT * FROM AREA`。

1. 如果以前从未在逻辑应用中用过 DB2 操作，请查看[添加 DB2 操作 - 获取表](#add-db2-action)部分中的步骤，但应该添加“获取多行”操作，然后返回此处以继续。

   添加“获取多行”操作后，示例逻辑应用如下所示：

   ![“获取多行”操作](./media/connectors-create-api-db2/db2-get-rows-action.png)

1. 打开“表名称”列表，然后选择所需的表，在本示例中为“AREA”：

   ![选择表](./media/connectors-create-api-db2/db2-get-rows-action-select-table.png)

1. 若要指定筛选器或查询结果，请选择“显示高级选项”。

1. 完成后，请在设计器工具栏上选择“保存”。

### <a name="view-output-rows"></a>查看输出行

若要手动运行逻辑应用，请在设计器工具栏上选择“运行”。 完成运行逻辑应用后，可以查看运行后的输出。

1. 在逻辑应用菜单中，选择“概述”。

1. 在“运行历史记录”部分中的“摘要”下，选择最近的运行，即列表中的第一项。

1. 在“逻辑应用运行”下，现在可以查看逻辑应用中每个步骤的状态、输入和输出。
展开“获取多行”操作。

1. 若要查看输入，请选择“显示原始输入”。

1. 若要查看输出，请选择“显示原始输出”。

   输出包括指定表中的所有记录。

   ![查看输出行](./media/connectors-create-api-db2/db2-connector-get-rows-outputs.png)

## <a name="insert-row"></a>插入行

若要将一条记录添加到 DB2 数据库表，请在逻辑应用中使用“插入行”操作。 此操作运行 DB2 `INSERT` 语句，例如 `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`。

1. 如果以前从未在逻辑应用中用过 DB2 操作，请查看[添加 DB2 操作 - 获取表](#add-db2-action)部分中的步骤，但应该添加“插入行”操作，然后返回此处以继续。

   添加“插入行”操作后，示例逻辑应用如下所示：

   ![“插入行”操作](./media/connectors-create-api-db2/db2-insert-row-action.png)

1. 指定所有必需属性 (*) 的值。 选择一个表后，该操作会显示特定于该表中的记录的相关属性。

   本示例的属性如下：

   | properties | 必选 | 说明 |
   |----------|----------|-------------|
   | **表名称** | 是 | 要将记录添加到的表，例如“AREA” |
   | **地区 ID** | 是 | 要添加的地区的 ID，例如“99999” |
   | **地区说明** | 是 | 要添加的地区的说明，例如“地区 99999” |
   | **区域 ID** | 是 | 要添加的区域的 ID，例如“102” |
   |||| 

   例如：

   ![选择表](./media/connectors-create-api-db2/db2-insert-row-action-select-table.png)

1. 完成后，请在设计器工具栏上选择“保存”。

### <a name="view-insert-row-outputs"></a>查看“插入行”的输出

若要手动运行逻辑应用，请在设计器工具栏上选择“运行”。 完成运行逻辑应用后，可以查看运行后的输出。

1. 在逻辑应用菜单中，选择“概述”。

1. 在“运行历史记录”部分中的“摘要”下，选择最近的运行，即列表中的第一项。

1. 在“逻辑应用运行”下，现在可以查看逻辑应用中每个步骤的状态、输入和输出。
展开“插入行”操作。

1. 若要查看输入，请选择“显示原始输入”。

1. 若要查看输出，请选择“显示原始输出”。

   输出包括已添加到指定表中的记录。

   ![查看输出和插入的行](./media/connectors-create-api-db2/db2-connector-insert-row-outputs.png)

## <a name="update-row"></a>更新行

若要更新 DB2 数据库表中的一条记录，请在逻辑应用中使用“更新行”操作。 此操作运行 DB2 `UPDATE` 语句，例如 `UPDATE AREA SET AREAID = '99999', AREADESC = 'Updated 99999', REGIONID = 102)`。

1. 如果以前从未在逻辑应用中用过 DB2 操作，请查看[添加 DB2 操作 - 获取表](#add-db2-action)部分中的步骤，但应该添加“更新行”操作，然后返回此处以继续。

   添加“更新行”操作后，示例逻辑应用如下所示：

   ![“更新行”操作](./media/connectors-create-api-db2/db2-update-row-action.png)

1. 指定所有必需属性 (*) 的值。 选择一个表后，该操作会显示特定于该表中的记录的相关属性。

   本示例的属性如下：

   | properties | 必选 | 说明 |
   |----------|----------|-------------|
   | **表名称** | 是 | 要在其中更新记录的表，例如“AREA” |
   | **行 ID** | 是 | 要更新的记录的 ID，例如“99999” |
   | **地区 ID** | 是 | 新地区 ID，例如“99999” |
   | **地区说明** | 是 | 新地区说明，例如“已更新 99999” |
   | **区域 ID** | 是 | 新区域 ID，例如“102” |
   ||||

   例如：

   ![选择表](./media/connectors-create-api-db2/db2-update-row-action-select-table.png)

1. 完成后，请在设计器工具栏上选择“保存”。

### <a name="view-update-row-outputs"></a>查看“更新行”的输出

若要手动运行逻辑应用，请在设计器工具栏上选择“运行”。 完成运行逻辑应用后，可以查看运行后的输出。

1. 在逻辑应用菜单中，选择“概述”。

1. 在“运行历史记录”部分中的“摘要”下，选择最近的运行，即列表中的第一项。

1. 在“逻辑应用运行”下，现在可以查看逻辑应用中每个步骤的状态、输入和输出。
展开“更新行”操作。

1. 若要查看输入，请选择“显示原始输入”。

1. 若要查看输出，请选择“显示原始输出”。

   输出包括已在指定表中更新的记录。

   ![查看输出和更新的行](./media/connectors-create-api-db2/db2-connector-update-row-outputs.png)

## <a name="delete-row"></a>删除行

若要删除 DB2 数据库表中的一条记录，请在逻辑应用中使用“删除行”操作。 此操作运行 DB2 `DELETE` 语句，例如 `DELETE FROM AREA WHERE AREAID = '99999'`。

1. 如果以前从未在逻辑应用中用过 DB2 操作，请查看[添加 DB2 操作 - 获取表](#add-db2-action)部分中的步骤，但应该添加“删除行”操作，然后返回此处以继续。

   添加“删除行”操作后，示例逻辑应用如下所示：

   ![“删除行”操作](./media/connectors-create-api-db2/db2-delete-row-action.png)

1. 指定所有必需属性 (*) 的值。 选择一个表后，该操作会显示特定于该表中的记录的相关属性。

   本示例的属性如下：

   | properties | 必选 | 说明 |
   |----------|----------|-------------|
   | **表名称** | 是 | 要在其中删除记录的表，例如“AREA” |
   | **行 ID** | 是 | 要删除的记录的 ID，例如“99999” |
   ||||

   例如：

   ![选择表](./media/connectors-create-api-db2/db2-delete-row-action-select-table.png)

1. 完成后，请在设计器工具栏上选择“保存”。

### <a name="view-delete-row-outputs"></a>查看“删除行”的输出

若要手动运行逻辑应用，请在设计器工具栏上选择“运行”。 完成运行逻辑应用后，可以查看运行后的输出。

1. 在逻辑应用菜单中，选择“概述”。

1. 在“运行历史记录”部分中的“摘要”下，选择最近的运行，即列表中的第一项。

1. 在“逻辑应用运行”下，现在可以查看逻辑应用中每个步骤的状态、输入和输出。
展开“删除行”操作。

1. 若要查看输入，请选择“显示原始输入”。

1. 若要查看输出，请选择“显示原始输出”。

   输出不再包括已从指定表中删除的记录。

   ![查看不包括已删除行的输出](./media/connectors-create-api-db2/db2-connector-delete-row-outputs.png)

## <a name="connector-reference"></a>连接器参考

有关此连接器的更多技术详细信息，如连接器的 Swagger 文件所述的触发器、操作和限制，请参阅[连接器的参考页](https://docs.microsoft.com/connectors/db2/)。

> [!NOTE]
> 对于[integration service 环境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中的逻辑应用，此连接器的 ise 标记版本会改用[ise 消息限制](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
