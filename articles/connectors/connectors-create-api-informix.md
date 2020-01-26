---
title: 连接到 IBM Informix 数据库
description: 使用 Azure 逻辑应用自动管理在 IBM Informix 中存储的资源的任务和工作流
services: logic-apps
ms.suite: integration
author: gplarsen
ms.author: plarsen
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/07/2020
tags: connectors
ms.openlocfilehash: dccb715c974037b4e3080f3e51576feae34c03df
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2020
ms.locfileid: "76757962"
---
# <a name="manage-ibm-informix-database-resources-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用管理 IBM Informix 数据库资源

使用[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[Informix 连接器](/connectors/informix/)，可以创建管理 IBM Informix 数据库中的资源的自动化任务和工作流。 此连接器包括通过 TCP/IP 网络与远程 Informix 服务器计算机通信的 Microsoft 客户端，包括基于云的数据库，例如，在使用[本地数据网关](../logic-apps/logic-apps-gateway-connection.md)时，Azure 虚拟化中运行的 WINDOWS 的 IBM Informix 和本地数据库。 如果配置为支持分布式关系数据库体系结构（DRDA）客户端连接，则可以连接到这些 Informix 平台和版本：

* IBM Informix 12.1
* IBM Informix 11.7

이 항목에서는 논리 앱에서 커넥터를 사용하여 데이터베이스 작업을 처리하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 对于本地数据库，请在本地计算机上[下载并安装本地数据网关](../logic-apps/logic-apps-gateway-install.md)，然后[在 Azure 门户中创建 Azure 数据网关资源](../logic-apps/logic-apps-gateway-connection.md)。

* 需要访问 Informix 数据库的逻辑应用。 此连接器仅提供操作，因此逻辑应用必须已以触发器开头，例如，[重复执行触发器](../connectors/connectors-native-recurrence.md)。 

## <a name="add-an-informix-action"></a>添加 Informix 操作

1. [Azure Portal](https://portal.azure.com)의 Logic App 디자이너에서 논리 앱을 엽니다(아직 열려 있지 않은 경우).

1. 在要添加 Informix 操作的步骤下，选择 "**新建步骤**"。

   기존 단계 간에 작업을 추가하려면 연결 화살표 위로 마우스를 이동합니다. 选择出现的加号（ **+** ），然后选择 "**添加操作**"。

1. 검색 상자에서 필터로 `informix`을 입력합니다. 从 "操作" 列表中，选择所需的操作，例如：

   ![选择要运行的 Informix 操作](./media/connectors-create-api-informix/select-informix-connector-action.png)

   连接器提供这些操作，这些操作将运行相应的数据库操作：

   * 获取表-使用 `CALL` 语句列出数据库表
   * 获取行-通过使用 `SELECT *` 语句读取所有行
   * 使用 `SELECT WHERE` 语句获取行读取行
   * 使用 `INSERT` 语句添加行
   * 使用 `UPDATE` 语句编辑行
   * 使用 `DELETE` 语句删除行

1. 如果系统提示你提供 Informix 数据库的连接详细信息，请按照[创建连接的步骤进行](#create-connection)操作，然后继续下一步。

1. 提供选定操作的信息：

   | 실행력 | Description | 属性和说明 |
   |--------|-------------|-----------------------------|
   | **获取表** | 通过运行 Informix CALL 语句列出数据库表。 | 없음 |
   | **获取行** | 通过运行 Informix `SELECT *` 语句获取指定表中的所有行。 | **表名**：所需的 Informix 表的名称 <p><p>若要向此操作添加其他属性，请从 "**添加新参数**" 列表中选择它们。 有关详细信息，请参阅[连接器的参考主题](/connectors/informix/)。 |
   | **获取行** | 通过运行 Informix `SELECT WHERE` 语句从指定的表中提取行。 | - **表名称**：所需的 Informix 表的名称 <br>- **行 id**：行的唯一 ID，如 `9999` |
   | **插入行** | 通过运行 Informix `INSERT` 语句，将行添加到指定的 Informix 表中。 | - **表名称**：所需的 Informix 表的名称 <br>- **项**：包含要添加的值的行 |
   | **更新行** | 通过运行 Informix `UPDATE` 语句来更改指定 Informix 表中的行。 | - **表名称**：所需的 Informix 表的名称 <br>- **行 ID**：要更新的行的唯一 ID，例如 `9999` <br>- **行**：具有更新值的行，例如 `102` |
   | **删除行** | 通过运行 Informix `DELETE` 语句从指定的 Informix 表中删除行。 | - **表名称**：所需的 Informix 表的名称 <br>- **行 ID**：要删除的行的唯一 ID，例如 `9999` |
   ||||

1. 논리 앱을 저장합니다. 现在，请[测试逻辑应用](#test-logic-app)或继续构建逻辑应用。

<a name="create-connection"></a>

## <a name="connect-to-informix"></a>连接到 Informix

1. 如果逻辑应用连接到本地数据库，请选择 "**通过本地数据网关连接**"。

1. 提供此连接信息，然后选择 "**创建**"。

   | 속성 | JSON 속성 | 필수 | 예제 값 | Description |
   |----------|---------------|----------|---------------|-------------|
   | 연결 이름 | `name` | 예 | `informix-demo-connection` | 用于连接到 Informix 数据库的名称 |
   | 서버 | `server` | 예 | -Cloud： `informixdemo.cloudapp.net:9089` <br>-本地： `informixdemo:9089` | 采用 IPv4 或 IPv6 格式的 TCP/IP 地址或别名，后跟冒号和 TCP/IP 端口号 |
   | 데이터베이스 | `database` | 예 | `nwind` | DRDA 关系数据库名称（RDBNAM）或 Informix 数据库名称（dbname）。 Informix 接受128字节的字符串。 |
   | 인증 | `authentication` | 仅本地 | **Basic**或**Windows** （kerberos） | Informix 数据库所需的身份验证类型。 仅当选择 **"通过本地数据网关连接"** 时，才会显示此属性。 |
   | 사용자 이름 | `username` | 아닙니다. | <*数据库-* 用户名> | 数据库的用户名 |
   | 암호 | `password` | 아닙니다. | <*数据库-密码*> | 数据库的密码 |
   | 게이트웨이 | `gateway` | 仅本地 | -<*Azure-订阅*> <br>-<*Azure-本地-网关-资源*> | 在 Azure 门户中创建的本地数据网关的 Azure 订阅和 Azure 资源名称。 仅当选择 "**通过本地数据网关连接**" 时，才会显示**网关**属性和子属性。 |
   ||||||

   예:

   * **云数据库**

     ![云数据库连接信息](./media/connectors-create-api-informix/informix-cloud-connection.png)

   * **本地数据库**

     ![本地数据库连接信息](./media/connectors-create-api-informix/informix-on-premises-connection.png)

1. 논리 앱을 저장합니다.

<a name="test-logic-app"></a>

## <a name="test-your-logic-app"></a>논리 앱 테스트

1. 在逻辑应用设计器工具栏上，选择 "**运行**"。 逻辑应用运行后，你可以从该运行中查看输出。

1. 从逻辑应用的菜单中，选择 "**概述**"。 在 "概述" 窗格中的 "**摘要** > **运行历史记录**" 下，选择最近运行的。

1. 在 "**逻辑应用运行**" 下，选择 "**运行详细信息**"。

1. 从 "操作" 列表中，选择包含要查看的输出的操作，例如**Get_tables**。

   如果操作成功，则会将其 "**状态**" 属性标记为 "**成功**"。

1. 若要查看输入，请在 "**输入链接**" 下，选择 "URL" 链接。 若要查看输出，请在 "**输出链接**" 链接下，选择 "URL" 链接。 下面是一些示例输出：

   * **Get_tables**显示表的列表：

     !["获取表" 操作的输出](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

   * **Get_rows**显示行列表：

     !["获取行" 操作的输出](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

   * **Get_row**显示指定行：

     !["获取行" 操作的输出](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

   * **Insert_row**显示新行：

     !["插入行" 操作的输出](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

   * **Update_row**显示更新的行：

     !["更新行" 操作的输出](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

   * **Delete_row**显示已删除的行：

     !["删除行" 操作的输出](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

有关由连接器的 Swagger 说明描述的触发器、操作和限制的技术详细信息，请查看[连接器的参考页](/connectors/informix/)。

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](apis-list.md)에 대해 알아봅니다.
