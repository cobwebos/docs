---
title: 连接到 DB2 - Azure 逻辑应用 | Microsoft Docs
description: 使用 DB2 REST API 和 Azure 逻辑应用管理资源
author: gplarsen
manager: jeconnoc
ms.author: plarsen
ms.date: 09/26/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, estfan
ms.suite: integration
tags: connectors
ms.openlocfilehash: 507bc48b6b775d6a6fb5f855210d33520e187a74
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295085"
---
# <a name="get-started-with-the-db2-connector"></a>DB2 连接器入门
Microsoft DB2 连接器可将逻辑应用连接到在 IBM DB2 数据库中存储的资源。 此连接器包括要在 TCP/IP 网络上与远程 DB2 服务器计算机通信的 Microsoft 客户端。 这包括云数据库（如在 Azure 虚拟化中运行的 Windows IBM Bluemix dashDB 或 IBM DB2）和使用本地数据网关的本地数据库。 请参阅 IBM DB2 平台和版本（在本主题中）的[受支持列表](connectors-create-api-db2.md#supported-db2-platforms-and-versions)。

DB2 连接器支持以下数据库操作：

* 列出数据库表
* 使用 SELECT 读取一行
* 使用 SELECT 读取全部行
* 使用 INSERT 添加一行
* 使用 UPDATE 更改一行
* 使用 DELETE 删除一行

本主题介绍了如何在逻辑应用中使用连接器来处理数据库操作。

若要了解有关逻辑应用的详细信息，请参阅[创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="available-actions"></a>可用操作
DB2 连接器支持以下逻辑应用操作：

* GetTables
* GetRow
* GetRows
* InsertRow
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>列出表
为任何操作创建逻辑应用包含通过 Microsoft Azure 门户执行的许多步骤。

在逻辑应用内，可添加在 DB2 数据库中列出表的操作。 该操作指示连接器处理 DB2 架构语句（如 `CALL SYSIBM.SQLTABLES`）。

### <a name="create-a-logic-app"></a>创建逻辑应用
1. 在 **Azure 开始面板**中，依次选择“+”（加号）、“Web + 移动”和“逻辑应用”。
2. 输入“名称”（例如 `Db2getTables`）、“订阅”、“资源组”、“位置”和“应用服务计划”。 选择“固定到仪表板”，并选择“创建”。

### <a name="add-a-trigger-and-action"></a>添加触发器和操作
1. 在“逻辑应用设计器”中的“模板”列表中，选择“空白逻辑应用”。
2. 在“触发器”列表中，选择“定期”。 
3. 在“定期”触发器中，选择“编辑”、选择“频率”下拉列表以选择“天”，并设置“间隔”以键入“7”。  
4. 选择“+ 新步骤”框，并选择“添加操作”。
5. 在“操作”列表中，在“搜索更多操作”编辑框中键入 `db2`，并选择“DB2 - 获取表(预览)”。
   
   ![](./media/connectors-create-api-db2/Db2connectorActions.png)  
6. 在“DB2 - 获取表”配置窗格中，选中“复选框”以启用“通过本地数据网关连接”。 请注意，这些设置从云更改到本地。
   
   * 以地址或别名冒号端口号的形式键入“服务器”的值。 例如，键入 `ibmserver01:50000`。
   * 键入“数据库”的值。 例如，键入 `nwind`。
   * 选择“身份验证”的值。 例如，选择“Basic”。
   * 键入“用户名”的值。 例如，键入 `db2admin`。
   * 键入“密码”的值。 例如，键入 `Password1`。
   * 选择“网关”的值。 例如，选择“datagateway01”。
7. 选择“创建”，并选择“保存”。 
   
    ![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)
8. 在“Db2getTables”边栏选项卡中，在“摘要”下面的“所有运行”列表中，选择第一个列出项（最近运行）。
9. 在“逻辑应用运行”边栏选项卡中，选择“运行详细信息”。 在“操作”列表内，选择“Get_tables”。 设置“状态”的值，该值应为 **Succeeded**。 选择“输入链接”查看输入。 选择“输出链接”并查看输出；其中应包括表列表。
   
   ![](./media/connectors-create-api-db2/Db2connectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>创建连接
此连接器支持使用以下连接属性连接到本地托管的数据库和云中。 

| 属性 | 说明 |
| --- | --- |
| server |必需。 接受表示 TCP/IP 地址或别名的字符串值，该地址或别名采用 IPv4 或 IPv6 格式，后跟（冒号分隔）TCP/IP 端口号。 |
| database |必需。 接受表示 DRDA 相关数据库名称 (RDBNAM) 的字符串值。 DB2 for z/OS 接受 16 个字节字符串（数据库也称为 IBM DB2 for z/OS 位置）。 DB2 for i5/OS 接受 18 个字节字符串（数据库也称为 IBM DB2 for i 相关数据库）。 DB2 for LUW 接受 8 个字节字符串。 |
| authentication |可选。 接受 Basic 或 Windows (kerberos) 列表项值。 |
| username |必需。 接受字符串值。 DB2 for z/OS 接受 8 个字节字符串。 DB2 for i 接受 10 个字节字符串。 DB2 for Linux 或 UNIX 接受 8 个字节字符串。 DB2 for Windows 接受 30 个字节字符串。 |
| password |必需。 接受字符串值。 |
| gateway |必需。 接受列表项值，表示已在存储组内定义为逻辑应用的本地数据网关。 |

## <a name="create-the-on-premises-gateway-connection"></a>创建本地网关连接
此连接器可以使用本地网关访问本地 DB2 数据库。 有关详细信息，请参阅网关主题。 

1. 在“网关”配置窗格中，选中“复选框”以启用“通过网关连接”。 请注意，这些设置从云更改到本地。
2. 以地址或别名冒号端口号的形式键入“服务器”的值。 例如，键入 `ibmserver01:50000`。
3. 键入“数据库”的值。 例如，键入 `nwind`。
4. 选择“身份验证”的值。 例如，选择“Basic”。
5. 键入“用户名”的值。 例如，键入 `db2admin`。
6. 键入“密码”的值。 例如，键入 `Password1`。
7. 选择“网关”的值。 例如，选择“datagateway01”。
8. 选择“创建”继续操作。 
   
    ![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>创建云连接
此连接器可访问云 DB2 数据库。 

1. 在“网关”配置窗格中，使“复选框”保持禁用（未单击）“通过网关连接”状态。 
2. 键入“连接名称”的值。 例如，键入 `hisdemo2`。
3. 以地址或别名冒号端口号的形式键入“DB2 服务器名称”的值。 例如，键入 `hisdemo2.cloudapp.net:50000`。
4. 键入“DB2 数据库名称”的值。 例如，键入 `nwind`。
5. 键入“用户名”的值。 例如，键入 `db2admin`。
6. 键入“密码”的值。 例如，键入 `Password1`。
7. 选择“创建”继续操作。 
   
    ![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>使用 SELECT 获取全部行
可定义逻辑应用操作，获取 DB2 表中的全部行。 该操作指示连接器处理 DB2 SELECT 语句（如 `SELECT * FROM AREA`）。

### <a name="create-a-logic-app"></a>创建逻辑应用
1. 在 **Azure 开始面板**中，依次选择“+”（加号）、“Web + 移动”和“逻辑应用”。
2. 输入“名称”（例如 `Db2getRows`）、“订阅”、“资源组”、“位置”和“应用服务计划”。 选择“固定到仪表板”，并选择“创建”。

### <a name="add-a-trigger-and-action"></a>添加触发器和操作
1. 在“逻辑应用设计器”中的“模板”列表中，选择“空白逻辑应用”。
2. 在“触发器”列表中，选择“定期”。 
3. 在“定期”触发器中，选择“编辑”，再选择“频率”下拉列表以选择“天”，然后选择“间隔”以键入“7”。 
4. 选择“+ 新步骤”框，并选择“添加操作”。
5. 在“操作”列表中，在“搜索更多操作”编辑框中键入 `db2`，并选择“DB2 - 获取行(预览)”。
6. 在“获取行(预览)”操作中，选择“更改连接”。
7. 在“连接”配置窗格中，选择“新建”。 
   
    ![](./media/connectors-create-api-db2/Db2connectorNewConnection.png)
8. 在“网关”配置窗格中，使“复选框”保持禁用（未单击）“通过网关连接”状态。
   
   * 键入“连接名称”的值。 例如，键入 `HISDEMO2`。
   * 以地址或别名冒号端口号的形式键入“DB2 服务器名称”的值。 例如，键入 `HISDEMO2.cloudapp.net:50000`。
   * 键入“DB2 数据库名称”的值。 例如，键入 `NWIND`。
   * 键入“用户名”的值。 例如，键入 `db2admin`。
   * 键入“密码”的值。 例如，键入 `Password1`。
9. 选择“创建”继续操作。
   
    ![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)
10. 在“表单名称”列表中，选择“下拉箭头”，并选择“AREA”。
11. 可以选择“显示高级选项”指定查询选项。
12. 选择“保存”。 
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowsTableName.png)
13. 在“Db2getRows”边栏选项卡中，在“摘要”下面的“所有运行”列表中，选择第一个列出项（最近运行）。
14. 在“逻辑应用运行”边栏选项卡中，选择“运行详细信息”。 在“操作”列表内，选择“Get_rows”。 设置“状态”的值，该值应为 **Succeeded**。 选择“输入链接”查看输入。 选择“输出链接”并查看输出；其中应包括行列表。
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>使用 INSERT 添加一行
可定义逻辑应用操作，在 DB2 表中添加一行。 该操作指示连接器处理 DB2 INSERT 语句（如 `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`）。

### <a name="create-a-logic-app"></a>创建逻辑应用
1. 在 **Azure 开始面板**中，依次选择“+”（加号）、“Web + 移动”和“逻辑应用”。
2. 输入“名称”（例如 `Db2insertRow`）、“订阅”、“资源组”、“位置”和“应用服务计划”。 选择“固定到仪表板”，并选择“创建”。

### <a name="add-a-trigger-and-action"></a>添加触发器和操作
1. 在“逻辑应用设计器”中的“模板”列表中，选择“空白逻辑应用”。
2. 在“触发器”列表中，选择“定期”。 
3. 在“定期”触发器中，选择“编辑”，再选择“频率”下拉列表以选择“天”，然后选择“间隔”以键入“7”。 
4. 选择“+ 新步骤”框，并选择“添加操作”。
5. 在“操作”列表中，在“搜索更多操作”编辑框中键入 **db2**，并选择“DB2 - 插入行(预览)”。
6. 在“DB2 - 插入行(预览)”操作中，选择“更改连接”。 
7. 在“连接”配置窗格中，选择一个连接。 例如，选择“hisdemo2”。
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. 在“表单名称”列表中，选择“下拉箭头”，并选择“AREA”。
9. 输入所有所需列的值（查看红色星号）。 例如，键入“AREAID”的 `99999`、键入 `Area 99999` 并键入“REGIONID”的 `102`。 
10. 选择“保存”。
    
    ![](./media/connectors-create-api-db2/Db2connectorInsertRowValues.png)
11. 在“Db2insertRow”边栏选项卡中，在“摘要”下面的“所有运行”列表中，选择第一个列出项（最近运行）。
12. 在“逻辑应用运行”边栏选项卡中，选择“运行详细信息”。 在“操作”列表内，选择“Get_rows”。 设置“状态”的值，该值应为 **Succeeded**。 选择“输入链接”查看输入。 选择“输出链接”并查看输出；其中应包括新行。
    
    ![](./media/connectors-create-api-db2/Db2connectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>使用 SELECT 获取一行
可定义逻辑应用操作，在 DB2 表中获取一行。 该操作指示连接器处理 DB2 SELECT WHERE 语句（如 `SELECT FROM AREA WHERE AREAID = '99999'`）。

### <a name="create-a-logic-app"></a>创建逻辑应用
1. 在 **Azure 开始面板**中，依次选择“+”（加号）、“Web + 移动”和“逻辑应用”。
2. 输入“名称”（例如“**Db2getRow**”）、“订阅”、“资源组”、“位置”和“应用服务计划”。 选择“固定到仪表板”，并选择“创建”。

### <a name="add-a-trigger-and-action"></a>添加触发器和操作
1. 在“逻辑应用设计器”中的“模板”列表中，选择“空白逻辑应用”。 
2. 在“触发器”列表中，选择“定期”。 
3. 在“定期”触发器中，选择“编辑”，再选择“频率”下拉列表以选择“天”，然后选择“间隔”以键入“7”。 
4. 选择“+ 新步骤”框，并选择“添加操作”。
5. 在“操作”列表中，在“搜索更多操作”编辑框中键入 **db2**，并选择“DB2 - 获取行(预览)”。
6. 在“获取行(预览)”操作中，选择“更改连接”。 
7. 在“连接”配置窗格中，选择一个现有连接。 例如，选择“hisdemo2”。
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. 在“表单名称”列表中，选择“下拉箭头”，并选择“AREA”。
9. 输入所有所需列的值（查看红色星号）。 例如，键入“AREAID”的 `99999`。 
10. 可以选择“显示高级选项”指定查询选项。
11. 选择“保存”。 
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowValues.png)
12. 在“Db2getRow”边栏选项卡中，在“摘要”下面的“所有运行”列表中，选择第一个列出项（最近运行）。
13. 在“逻辑应用运行”边栏选项卡中，选择“运行详细信息”。 在“操作”列表内，选择“Get_rows”。 设置“状态”的值，该值应为 **Succeeded**。 选择“输入链接”查看输入。 选择“输出链接”并查看输出；其中应包括行。
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>使用 UPDATE 更改一行
可定义逻辑应用操作，在 DB2 表中更改一行。 该操作指示连接器处理 DB2 UPDATE 语句（如 `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`）。

### <a name="create-a-logic-app"></a>创建逻辑应用
1. 在 **Azure 开始面板**中，依次选择“+”（加号）、“Web + 移动”和“逻辑应用”。
2. 输入“名称”（例如 `Db2updateRow`）、“订阅”、“资源组”、“位置”和“应用服务计划”。 选择“固定到仪表板”，并选择“创建”。

### <a name="add-a-trigger-and-action"></a>添加触发器和操作
1. 在“逻辑应用设计器”中的“模板”列表中，选择“空白逻辑应用”。
2. 在“触发器”列表中，选择“定期”。 
3. 在“定期”触发器中，选择“编辑”，再选择“频率”下拉列表以选择“天”，然后选择“间隔”以键入“7”。 
4. 选择“+ 新步骤”框，并选择“添加操作”。
5. 在“操作”列表中，在“搜索更多操作”编辑框中键入 **db2**，并选择“DB2 - 更新行(预览)”。
6. 在“DB2 - 更新行(预览)”操作中，选择“更改连接”。 
7. 在“连接”配置窗格中，选择一个现有连接。 例如，选择“hisdemo2”。
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. 在“表单名称”列表中，选择“下拉箭头”，并选择“AREA”。
9. 输入所有所需列的值（查看红色星号）。 例如，键入“AREAID”的 `99999`、键入 `Updated 99999` 并键入“REGIONID”的 `102`。 
10. 选择“保存”。 
    
    ![](./media/connectors-create-api-db2/Db2connectorUpdateRowValues.png)
11. 在“Db2updateRow”边栏选项卡中，在“摘要”下面的“所有运行”列表中，选择第一个列出项（最近运行）。
12. 在“逻辑应用运行”边栏选项卡中，选择“运行详细信息”。 在“操作”列表内，选择“Get_rows”。 设置“状态”的值，该值应为 **Succeeded**。 选择“输入链接”查看输入。 选择“输出链接”并查看输出；其中应包括新行。
    
    ![](./media/connectors-create-api-db2/Db2connectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>使用 DELETE 删除一行
可定义逻辑应用操作，在 DB2 表中删除一行。 该操作指示连接器处理 DB2 DELETE 语句（如 `DELETE FROM AREA WHERE AREAID = '99999'`）。

### <a name="create-a-logic-app"></a>创建逻辑应用
1. 在 **Azure 开始面板**中，依次选择“+”（加号）、“Web + 移动”和“逻辑应用”。
2. 输入“名称”（例如 `Db2deleteRow`）、“订阅”、“资源组”、“位置”和“应用服务计划”。 选择“固定到仪表板”，并选择“创建”。

### <a name="add-a-trigger-and-action"></a>添加触发器和操作
1. 在“逻辑应用设计器”中的“模板”列表中，选择“空白逻辑应用”。 
2. 在“触发器”列表中，选择“定期”。 
3. 在“定期”触发器中，选择“编辑”，再选择“频率”下拉列表以选择“天”，然后选择“间隔”以键入“7”。 
4. 选择“+ 新步骤”框，并选择“添加操作”。
5. 在“操作”列表中，在“搜索更多操作”编辑框中选择 **db2**，并选择“DB2 - 删除行(预览)”。
6. 在“DB2 - 删除行(预览)”操作中，选择“更改连接”。 
7. 在“连接”配置窗格中，选择一个现有连接。 例如，选择“hisdemo2”。
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. 在“表单名称”列表中，选择“下拉箭头”，并选择“AREA”。
9. 输入所有所需列的值（查看红色星号）。 例如，键入“AREAID”的 `99999`。 
10. 选择“保存”。 
    
    ![](./media/connectors-create-api-db2/Db2connectorDeleteRowValues.png)
11. 在“Db2deleteRow”边栏选项卡中，在“摘要”下面的“所有运行”列表中，选择第一个列出项（最近运行）。
12. 在“逻辑应用运行”边栏选项卡中，选择“运行详细信息”。 在“操作”列表内，选择“Get_rows”。 设置“状态”的值，该值应为 **Succeeded**。 选择“输入链接”查看输入。 选择“输出链接”并查看输出；其中应包括删除的行。
    
    ![](./media/connectors-create-api-db2/Db2connectorDeleteRowOutputs.png)

## <a name="supported-db2-platforms-and-versions"></a>支持的 DB2 平台和版本
此连接器支持以下 IBM DB2 平台和版本，以及支持分布式关系数据库结构 (DRDA) SQL 访问管理器 (SQLAM) 版本 10 和 11 的 IBM DB2 兼容产品（例如 IBM Bluemix dashDB）：

* IBM DB2 for z/OS 11.1
* IBM DB2 for z/OS 10.1
* IBM DB2 for i 7.3
* IBM DB2 for i 7.2
* IBM DB2 for i 7.1
* IBM DB2 for LUW 11
* IBM DB2 for LUW 10.5

## <a name="connector-specific-details"></a>特定于连接器的详细信息

在[连接器详细信息](/connectors/db2/)中查看在 Swagger 中定义的触发器和操作，并查看限制。 

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 在我们的 [API 列表](apis-list.md)中了解逻辑应用中的其他可用连接器。

