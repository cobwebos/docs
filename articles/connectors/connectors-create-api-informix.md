---
title: "在逻辑应用中添加 Informix 连接器 | Microsoft Docs"
description: "使用 REST API 参数的 Informix 连接器概述"
services: 
documentationcenter: 
author: gplarsen
manager: anneta
editor: 
tags: connectors
ms.assetid: ca2393f0-3073-4dc2-8438-747f5bc59689
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/26/2016
ms.author: plarsen; ladocs
ms.openlocfilehash: b2e755b5b1b4939eac90ac55ba8398c5687124c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-informix-connector"></a>Informix 连接器入门
Microsoft Informix 连接器可将逻辑应用连接到存储在 IBM Informix 数据库中的资源。 Informix 连接器包括要在 TCP/IP 网络上与远程 Informix 服务器计算机通信的 Microsoft 客户端。 这包括云数据库（如在 Azure 虚拟化中运行的 Windows IBM Informix）和使用本地数据网关的本地数据库。 请参阅 IBM Informix 平台和版本（在本主题中）的[受支持列表](connectors-create-api-informix.md#supported-informix-platforms-and-versions)。

此连接器支持以下数据库操作：

* 列出数据库表
* 使用 SELECT 读取一行
* 使用 SELECT 读取全部行
* 使用 INSERT 添加一行
* 使用 UPDATE 更改一行
* 使用 DELETE 删除一行

本主题介绍了如何在逻辑应用中使用连接器来处理数据库操作。

若要了解有关逻辑应用的详细信息，请参阅[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="available-actions"></a>可用操作
此连接器支持以下逻辑应用操作：

* Getables
* GetRow
* GetRows
* InsertRow
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>列出表
为任何操作创建逻辑应用包含通过 Microsoft Azure 门户执行的许多步骤。

在逻辑应用内，可添加在 Informix 数据库中列出表的操作。 该操作指示连接器处理 Informix 架构语句（如 `CALL SYSIBM.SQLTABLES`）。

### <a name="create-a-logic-app"></a>创建逻辑应用
1. 在 **Azure 开始面板**中，依次选择“+”（加号）、“Web + 移动”和“逻辑应用”。
2. 输入“名称”（例如 `InformixgetTables`）、“订阅”、“资源组”、“位置”和“应用服务计划”。 选择“固定到仪表板”，并选择“创建”。

### <a name="add-a-trigger-and-action"></a>添加触发器和操作
1. 在“逻辑应用设计器”中的“模板”列表中，选择“空白逻辑应用”。
2. 在“触发器”列表中，选择“定期”。 
3. 在“定期”触发器中，选择“编辑”，再选择“频率”下拉列表以选择“天”，然后选择“间隔”以键入“7”。  
4. 选择“+ 新步骤”框，并选择“添加操作”。
5. 在“操作”列表中，在“搜索更多操作”编辑框中键入 **informix**，并选择“Informix - 获取表(预览)”。
   
   ![](./media/connectors-create-api-informix/InformixconnectorActions.png)  
6. 在“Informix - 获取表”配置窗格中，选中“复选框”以启用“通过本地数据网关连接”。 请注意，这些设置从云更改到本地。
   
   * 以地址或别名冒号端口号的形式键入“服务器”的值。 例如，键入 `ibmserver01:9089`。
   * 键入“数据库”的值。 例如，键入 `nwind`。
   * 选择“身份验证”的值。 例如，选择“Basic”。
   * 键入“用户名”的值。 例如，键入 `informix`。
   * 键入“密码”的值。 例如，键入 `Password1`。
   * 选择“网关”的值。 例如，选择“datagateway01”。
7. 选择“创建”，并选择“保存”。 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)
8. 在“InformixgetTables”边栏选项卡中，在“摘要”下面的“所有运行”列表中，选择第一个列出项（最近运行）。
9. 在“逻辑应用运行”边栏选项卡中，选择“运行详细信息”。 在“操作”列表内，选择“Get_tables”。 设置“状态”的值，该值应为 **Succeeded**。 选择“输入链接”查看输入。 选择“输出链接”并查看输出；其中应包括表列表。
   
   ![](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>创建连接
此连接器支持使用以下连接属性连接到本地数据库和云中。 

| 属性 | 说明 |
| --- | --- |
| server |必需。 接受表示 TCP/IP 地址或别名的字符串值，该地址或别名采用 IPv4 或 IPv6 格式，后跟（冒号分隔）TCP/IP 端口号。 |
| database |必需。 接受表示 DRDA 相关数据库名称 (RDBNAM) 的字符串值。 Informix 接受 128 个字节字符串（数据库称为 IBM Informix 数据库名称 (dbname)）。 |
| authentication |可选。 接受 Basic 或 Windows (kerberos) 列表项值。 |
| username |必需。 接受字符串值。 |
| password |必需。 接受字符串值。 |
| gateway |必需。 接受列表项值，表示已在存储组内定义为逻辑应用的本地数据网关。 |

## <a name="create-the-on-premises-gateway-connection"></a>创建本地网关连接
此连接器可以使用本地数据网关访问本地 Informix 数据库。 有关详细信息，请参阅网关主题。 

1. 在“网关”配置窗格中，选中“复选框”以启用“通过网关连接”。 请参阅这些设置从云更改到本地。
2. 以地址或别名冒号端口号的形式键入“服务器”的值。 例如，键入 `ibmserver01:9089`。
3. 键入“数据库”的值。 例如，键入 `nwind`。
4. 选择“身份验证”的值。 例如，选择“Basic”。
5. 键入“用户名”的值。 例如，键入 `informix`。
6. 键入“密码”的值。 例如，键入 `Password1`。
7. 选择“网关”的值。 例如，选择“datagateway01”。
8. 选择“创建”继续操作。 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>创建云连接
此连接器可访问云 Informix 数据库。 

1. 在“网关”配置窗格中，使“复选框”保持禁用（未单击）“通过网关连接”状态。 
2. 键入“连接名称”的值。 例如，键入 `hisdemo2`。
3. 以地址或别名冒号端口号的形式键入“Informix 服务器名称”的值。 例如，键入 `hisdemo2.cloudapp.net:9089`。
4. 键入“Informix 数据库名称”的值。 例如，键入 `nwind`。
5. 键入“用户名”的值。 例如，键入 `informix`。
6. 键入“密码”的值。 例如，键入 `Password1`。
7. 选择“创建”继续操作。 
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>使用 SELECT 获取全部行
可创建逻辑应用操作，获取 Informix 表中的全部行。 该操作指示连接器处理 Informix SELECT 语句（如 `SELECT * FROM AREA`）。

### <a name="create-a-logic-app"></a>创建逻辑应用
1. 在 **Azure 开始面板**中，依次选择“+”（加号）、“Web + 移动”和“逻辑应用”。
2. 输入“名称”（例如“**InformixgetRows**”）、“订阅”、“资源组”、“位置”和“应用服务计划”。 选择“固定到仪表板”，并选择“创建”。

### <a name="add-a-trigger-and-action"></a>添加触发器和操作
1. 在“逻辑应用设计器”中的“模板”列表中，选择“空白逻辑应用”。
2. 在“触发器”列表中，选择“定期”。 
3. 在“定期”触发器中，选择“编辑”，再选择“频率”下拉列表以选择“天”，然后选择“间隔”以键入“7”。 
4. 选择“+ 新步骤”框，并选择“添加操作”。
5. 在“操作”列表中，在“搜索更多操作”编辑框中键入 **informix**，并选择“Informix - 获取行(预览)”。
6. 在“获取行(预览)”操作中，选择“更改连接”。
7. 在“连接”配置窗格中，选择“新建”。 
   
    ![](./media/connectors-create-api-informix/InformixconnectorNewConnection.png)
8. 在“网关”配置窗格中，使“复选框”保持禁用（未单击）“通过网关连接”状态。
   
   * 键入“连接名称”的值。 例如，键入 `HISDEMO2`。
   * 以地址或别名冒号端口号的形式键入“Informix 服务器名称”的值。 例如，键入 `HISDEMO2.cloudapp.net:9089`。
   * 键入“Informix 数据库名称”的值。 例如，键入 `NWIND`。
   * 键入“用户名”的值。 例如，键入 `informix`。
   * 键入“密码”的值。 例如，键入 `Password1`。
9. 选择“创建”继续操作。
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)
10. 在“表单名称”列表中，选择“下拉箭头”，并选择“AREA”。
11. 可以选择“显示高级选项”指定查询选项。
12. 选择“保存”。 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsTableName.png)
13. 在“InformixgetRows”边栏选项卡中，在“摘要”下面的“所有运行”列表中，选择第一个列出项（最近运行）。
14. 在“逻辑应用运行”边栏选项卡中，选择“运行详细信息”。 在“操作”列表内，选择“Get_rows”。 设置“状态”的值，该值应为 **Succeeded**。 选择“输入链接”查看输入。 选择“输出链接”并查看输出；其中应包括行列表。
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>使用 INSERT 添加一行
可创建逻辑应用操作，在 Informix 表中添加一行。 该操作指示连接器处理 Informix INSERT 语句（如 `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`）。

### <a name="create-a-logic-app"></a>创建逻辑应用
1. 在 **Azure 开始面板**中，依次选择“+”（加号）、“Web + 移动”和“逻辑应用”。
2. 输入“名称”（例如 `InformixinsertRow`）、“订阅”、“资源组”、“位置”和“应用服务计划”。 选择“固定到仪表板”，并选择“创建”。

### <a name="add-a-trigger-and-action"></a>添加触发器和操作
1. 在“逻辑应用设计器”中的“模板”列表中，选择“空白逻辑应用”。
2. 在“触发器”列表中，选择“定期”。 
3. 在“定期”触发器中，选择“编辑”，再选择“频率”下拉列表以选择“天”，然后选择“间隔”以键入“7”。 
4. 选择“+ 新步骤”框，并选择“添加操作”。
5. 在“操作”列表中，在“搜索更多操作”编辑框中键入 **informix**，并选择“Informix - 插入行(预览)”。
6. 在“获取行(预览)”操作中，选择“更改连接”。 
7. 在“连接”配置窗格中，选择一个连接。 例如，选择“hisdemo2”。
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. 在“表单名称”列表中，选择“下拉箭头”，并选择“AREA”。
9. 输入所有所需列的值（查看红色星号）。 例如，键入“AREAID”的 `99999`、键入 `Area 99999` 并键入“REGIONID”的 `102`。 
10. 选择“保存”。
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowValues.png)
11. 在“InformixinsertRow”边栏选项卡中，在“摘要”下面的“所有运行”列表中，选择第一个列出项（最近运行）。
12. 在“逻辑应用运行”边栏选项卡中，选择“运行详细信息”。 在“操作”列表内，选择“Get_rows”。 设置“状态”的值，该值应为 **Succeeded**。 选择“输入链接”查看输入。 选择“输出链接”并查看输出；其中应包括新行。
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>使用 SELECT 获取一行
可创建逻辑应用操作，在 Informix 表中提取一行。 该操作指示连接器处理 Informix SELECT WHERE 语句（如 `SELECT FROM AREA WHERE AREAID = '99999'`）。

### <a name="create-a-logic-app"></a>创建逻辑应用
1. 在 **Azure 开始面板**中，依次选择“+”（加号）、“Web + 移动”和“逻辑应用”。
2. 输入“名称”（例如 `InformixgetRow`）、“订阅”、“资源组”、“位置”和“应用服务计划”。 选择“固定到仪表板”，并选择“创建”。

### <a name="add-a-trigger-and-action"></a>添加触发器和操作
1. 在“逻辑应用设计器”中的“模板”列表中，选择“空白逻辑应用”。
2. 在“触发器”列表中，选择“定期”。 
3. 在“定期”触发器中，选择“编辑”，再选择“频率”下拉列表以选择“天”，然后选择“间隔”以键入“7”。 
4. 选择“+ 新步骤”框，并选择“添加操作”。
5. 在“操作”列表中，在“搜索更多操作”编辑框中键入 **informix**，并选择“Informix - 获取行(预览)”。
6. 在“获取行(预览)”操作中，选择“更改连接”。 
7. 在“连接”配置窗格中，选择一个现有连接。 例如，选择“hisdemo2”。
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. 在“表单名称”列表中，选择“下拉箭头”，并选择“AREA”。
9. 输入所有所需列的值（查看红色星号）。 例如，键入“AREAID”的 `99999`。 
10. 可以选择“显示高级选项”指定查询选项。
11. 选择“保存”。 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowValues.png)
12. 在“InformixgetRow”边栏选项卡中，在“摘要”下面的“所有运行”列表中，选择第一个列出项（最近运行）。
13. 在“逻辑应用运行”边栏选项卡中，选择“运行详细信息”。 在“操作”列表内，选择“Get_rows”。 设置“状态”的值，该值应为 **Succeeded**。 选择“输入链接”查看输入。 选择“输出链接”并查看输出；其中应包括行。
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>使用 UPDATE 更改一行
可创建逻辑应用操作，在 Informix 表中更改一行。 该操作指示连接器处理 Informix UPDATE 语句（如 `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`）。

### <a name="create-a-logic-app"></a>创建逻辑应用
1. 在 **Azure 开始面板**中，依次选择“+”（加号）、“Web + 移动”和“逻辑应用”。
2. 输入“名称”（例如 `InformixupdateRow`）、“订阅”、“资源组”、“位置”和“应用服务计划”。 选择“固定到仪表板”，并选择“创建”。

### <a name="add-a-trigger-and-action"></a>添加触发器和操作
1. 在“逻辑应用设计器”中的“模板”列表中，选择“空白逻辑应用”。
2. 在“触发器”列表中，选择“定期”。 
3. 在“定期”触发器中，选择“编辑”，再选择“频率”下拉列表以选择“天”，然后选择“间隔”以键入“7”。 
4. 选择“+ 新步骤”框，并选择“添加操作”。
5. 在“操作”列表中，在“搜索更多操作”编辑框中键入 **informix**，并选择“Informix - 更新行(预览)”。
6. 在“获取行(预览)”操作中，选择“更改连接”。 
7. 在“连接”配置窗格中，选择一个现有连接。 例如，选择“hisdemo2”。
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. 在“表单名称”列表中，选择“下拉箭头”，并选择“AREA”。
9. 输入所有所需列的值（查看红色星号）。 例如，键入“AREAID”的 `99999`、键入 `Updated 99999` 并键入“REGIONID”的 `102`。 
10. 选择“保存”。 
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowValues.png)
11. 在“InformixupdateRow”边栏选项卡中，在“摘要”下面的“所有运行”列表中，选择第一个列出项（最近运行）。
12. 在“逻辑应用运行”边栏选项卡中，选择“运行详细信息”。 在“操作”列表内，选择“Get_rows”。 设置“状态”的值，该值应为 **Succeeded**。 选择“输入链接”查看输入。 选择“输出链接”并查看输出；其中应包括新行。
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>使用 DELETE 删除一行
可创建逻辑应用操作，在 Informix 表中删除一行。 该操作指示连接器处理 Informix DELETE 语句（如 `DELETE FROM AREA WHERE AREAID = '99999'`）。

### <a name="create-a-logic-app"></a>创建逻辑应用
1. 在 **Azure 开始面板**中，依次选择“+”（加号）、“Web + 移动”和“逻辑应用”。
2. 输入“名称”（例如 `InformixdeleteRow`）、“订阅”、“资源组”、“位置”和“应用服务计划”。 选择“固定到仪表板”，并选择“创建”。

### <a name="add-a-trigger-and-action"></a>添加触发器和操作
1. 在“逻辑应用设计器”中的“模板”列表中，选择“空白逻辑应用”。
2. 在“触发器”列表中，选择“定期”。 
3. 在“定期”触发器中，选择“编辑”，再选择“频率”下拉列表以选择“天”，然后选择“间隔”以键入“7”。 
4. 选择“+ 新步骤”框，并选择“添加操作”。
5. 在“操作”列表中，在“搜索更多操作”编辑框中键入 **informix**，并选择“Informix - 删除行(预览)”。
6. 在“获取行(预览)”操作中，选择“更改连接”。 
7. 在“连接”配置窗格中，选择一个现有连接。 例如，选择“hisdemo2”。
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. 在“表单名称”列表中，选择“下拉箭头”，并选择“AREA”。
9. 输入所有所需列的值（查看红色星号）。 例如，键入“AREAID”的 `99999`。 
10. 选择“保存”。 
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowValues.png)
11. 在“InformixdeleteRow”边栏选项卡中，在“摘要”下面的“所有运行”列表中，选择第一个列出项（最近运行）。
12. 在“逻辑应用运行”边栏选项卡中，选择“运行详细信息”。 在“操作”列表内，选择“Get_rows”。 设置“状态”的值，该值应为 **Succeeded**。 选择“输入链接”查看输入。 选择“输出链接”并查看输出；其中应包括删除的行。
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="supported-informix-platforms-and-versions"></a>支持的 Informix 平台和版本
当配置为支持分布式关系数据库结构 (DRDA) 客户端连接时，此连接器支持以下 IBM Informix 版本。

* IBM Informix 12.1
* IBM Informix 11.7

## <a name="connector-specific-details"></a>特定于连接器的详细信息

在[连接器详细信息](/connectors/informix/)中查看在 Swagger 中定义的触发器和操作，并查看限制。 

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。 在我们的 [API 列表](apis-list.md)中了解逻辑应用中的其他可用连接器。

