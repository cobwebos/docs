---
title: 连接到 IBM Informix 数据库
description: 使用 Azure 逻辑应用自动执行管理存储在 IBM Informix 中的资源的任务和工作流
services: logic-apps
ms.suite: integration
author: gplarsen
ms.author: plarsen
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/07/2020
tags: connectors
ms.openlocfilehash: dccb715c974037b4e3080f3e51576feae34c03df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76757962"
---
# <a name="manage-ibm-informix-database-resources-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用管理 IBM Informix 数据库资源

使用[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[Informix 连接器](/connectors/informix/)，您可以创建自动任务和工作流来管理 IBM Informix 数据库中的资源。 此连接器包括 Microsoft 客户端，该客户端通过 TCP/IP 网络与远程 Informix 服务器计算机通信，包括基于云的数据库，如在 Azure 虚拟化中运行的 Windows 的 IBM Informix 和本地数据库，当您使用[本地数据网关](../logic-apps/logic-apps-gateway-connection.md)时。 如果这些 Informix 平台和版本配置为支持分布式关系数据库体系结构 （DRDA） 客户端连接，则可以连接到这些平台和版本：

* IBM Informix 12.1
* IBM Informix 11.7

本主题介绍了如何在逻辑应用中使用连接器来处理数据库操作。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 对于本地数据库，请在本地计算机上[下载并安装本地数据网关](../logic-apps/logic-apps-gateway-install.md)，然后在[Azure 门户中创建 Azure 数据网关资源](../logic-apps/logic-apps-gateway-connection.md)。

* 需要访问 Informix 数据库的逻辑应用。 此连接器仅提供操作，因此逻辑应用必须从触发器开始，例如["定期触发器](../connectors/connectors-native-recurrence.md)"。 

## <a name="add-an-informix-action"></a>添加 Informix 操作

1. 在 [Azure 门户](https://portal.azure.com)中，在逻辑应用设计器中打开你的逻辑应用（如果尚未打开）。

1. 在要添加 Informix 操作的步骤下，选择 **"新建步骤**"。

   若要在现有步骤之间添加操作，请将鼠标移到连接箭头上方。 选择显示的加号**+**（），然后选择 **"添加操作**"。

1. 在搜索框中，输入 `informix` 作为筛选器。 从操作列表中选择所需的操作，例如：

   ![选择要运行的 Informix 操作](./media/connectors-create-api-informix/select-informix-connector-action.png)

   连接器提供以下操作，这些操作运行相应的数据库操作：

   * 获取表 - 使用 语句`CALL`列出数据库表
   * 获取行 - 使用语句`SELECT *`读取所有行
   * 获取行 - 使用语句`SELECT WHERE`读取行
   * 使用`INSERT`语句添加行
   * 使用`UPDATE`语句编辑行
   * 使用`DELETE`语句删除行

1. 如果系统提示您为 Informix 数据库提供连接详细信息，请按照[步骤创建连接](#create-connection)，然后继续执行下一步。

1. 提供所选操作的信息：

   | 操作 | 描述 | 属性和说明 |
   |--------|-------------|-----------------------------|
   | **获取表** | 通过运行 Informix CALL 语句列出数据库表。 | 无 |
   | **获取行** | 通过运行 Informix`SELECT *`语句获取指定表中的所有行。 | **表名称**：所需的 Informix 表的名称 <p><p>要向此操作添加其他属性，请从 **"添加新参数**"列表中选择它们。 有关详细信息，请参阅[连接器的参考主题](/connectors/informix/)。 |
   | **获取行** | 通过运行 Informix`SELECT WHERE`语句从指定表获取行。 | - **表名称**：所需的 Informix 表的名称 <br>- **行 ID**：例如，行的唯一 ID，`9999` |
   | **插入行** | 通过运行 Informix 语句将行添加到指定的 Informix`INSERT`表。 | - **表名称**：所需的 Informix 表的名称 <br>- **项**： 具有要添加的值的行 |
   | **更新行** | 通过运行 Informix 语句更改指定 Informix`UPDATE`表中的行。 | - **表名称**：所需的 Informix 表的名称 <br>- **行 ID**：要更新的行的唯一 ID，例如，`9999` <br>- **行**：具有更新值的行，例如，`102` |
   | **删除行** | 通过运行 Informix 语句从指定的 Informix`DELETE`表中删除行。 | - **表名称**：所需的 Informix 表的名称 <br>- **行 ID**：要删除的行的唯一 ID，例如，`9999` |
   ||||

1. 保存逻辑应用。 现在，[测试逻辑应用](#test-logic-app)或继续构建逻辑应用。

<a name="create-connection"></a>

## <a name="connect-to-informix"></a>连接到 Informix

1. 如果逻辑应用连接到本地数据库，请选择 **"通过本地数据网关连接**"。

1. 提供此连接信息，然后选择 **"创建**"。

   | properties | JSON 属性 | 必选 | 示例值 | 描述 |
   |----------|---------------|----------|---------------|-------------|
   | 连接名称 | `name` | 是 | `informix-demo-connection` | 用于连接到 Informix 数据库的名称 |
   | 服务器 | `server` | 是 | - 云：`informixdemo.cloudapp.net:9089` <br>- 本地：`informixdemo:9089` | 采用 IPv4 或 IPv6 格式的 TCP/IP 地址或别名，后跟冒号和 TCP/IP 端口号 |
   | 数据库 | `database` | 是 | `nwind` | DRDA 关系数据库名称 （RDBNAM） 或 Informix 数据库名称 （dbname）。 Informix 接受 128 字节字符串。 |
   | 身份验证 | `authentication` | 仅限本地 | **基本**或**窗口**（kerberos） | Informix 数据库所需的身份验证类型。 仅当选择**通过本地数据网关进行连接**时，才会显示此属性。 |
   | 用户名 | `username` | 否 | <*数据库用户名*> | 数据库的用户名 |
   | 密码 | `password` | 否 | <*数据库密码*> | 数据库的密码 |
   | 网关 | `gateway` | 仅限本地 | - <*Azure 订阅*> <br>- <*Azure 本地数据网关资源*> | 在 Azure 门户中创建的本地数据网关的 Azure 订阅和 Azure 资源名称。 仅当选择**通过本地数据网关进行连接**时，才会显示**网关**属性和子属性。 |
   ||||||

   例如：

   * **云数据库**

     ![云数据库连接信息](./media/connectors-create-api-informix/informix-cloud-connection.png)

   * **本地数据库**

     ![本地数据库连接信息](./media/connectors-create-api-informix/informix-on-premises-connection.png)

1. 保存逻辑应用。

<a name="test-logic-app"></a>

## <a name="test-your-logic-app"></a>测试逻辑应用

1. 在逻辑应用设计器工具栏上，选择“运行”****。 逻辑应用运行后，您可以查看该运行中的输出。

1. 从逻辑应用的菜单中，选择 **"概述**"。 在"概述"窗格中，在 **"摘要** > **运行历史记录**"下，选择最近的运行。

1. 在**逻辑应用运行**下，选择 **"运行详细信息**"。

1. 从操作列表中，选择具有要查看的输出的操作，例如 **，Get_tables**。

   如果操作成功，则其**状态**属性将标记为 **"成功**"。

1. 要查看输入，请在**输入链接**下，选择 URL 链接。 要查看输出，请在 **"输出链接"** 下，选择 URL 链接。 下面是一些示例输出：

   * **Get_tables**显示表列表：

     !["获取表"操作中的输出](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

   * **Get_rows**显示行列表：

     ![来自"获取行"操作的输出](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

   * **Get_row**显示指定的行：

     !["获取行"操作的输出](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

   * **Insert_row**显示新行：

     !["插入行"操作的输出](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

   * **Update_row**显示更新的行：

     !["更新行"操作的输出](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

   * **Delete_row**显示已删除的行：

     !["删除行"操作的输出](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="connector-specific-details"></a>特定于连接器的详细信息

有关触发器、操作和限制的技术详细信息（由连接器的 Swagger 描述）描述，请查看[连接器的参考页](/connectors/informix/)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](apis-list.md)
