---
title: 在 Azure SQL Edge 中设置 IoT Edge 模块
description: 本 Azure SQL Edge 铁矿石杂质预测教程由三部分组成，在第二部分中，你将设置 IoT Edge 模块和连接。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sourabha, sstein
ms.date: 09/22/2020
ms.openlocfilehash: 7b2432fda70e8f9a5fa8bc64ede846d977672e9e
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90886485"
---
# <a name="set-up-iot-edge-modules-and-connections"></a>设置 IoT Edge 模块和连接

本 Azure SQL Edge 铁矿石杂质预测教程由三部分组成，在第二部分中，你将设置以下 IoT Edge 模块：

- Azure SQL Edge
- 数据生成器 IoT Edge 模块

## <a name="specify-container-registry-credentials"></a>指定容器注册表凭据

需要指定托管模块映像的容器注册表的凭据。 可在资源组中创建的容器注册表中找到它们。 导航到“访问密钥”部分。 请记下以下字段：

- 注册表名称
- 登录服务器
- 用户名
- 密码

现在，在 IoT Edge 模块中指定容器凭据。

1. 导航到在资源组中创建的 IoT 中心。

2. 在“自动设备管理”下的“IoT Edge”部分，单击“设备 ID”。 对于本教程，ID 是 `IronOrePredictionDevice`。

3. 选择“设置模块”部分。

4. 在“容器注册表凭据”下，输入以下值：

   _字段_|_值_
   -------|-------
   名称|注册表名称
   地址|登录服务器
   用户名|用户名
   密码|密码
  
## <a name="deploy-the-data-generator-module"></a>部署数据生成器模块

1. 在“自动设备管理”下的“IoT Edge”部分，单击“设备 ID”。 在本教程中，ID 为 `IronOrePredictionDevice`，然后单击“设置模块”。

2.  在“在设备上设置模块：”页的“IoT Edge 模块”部分下，单击“+ ADD”并选择“IoT Edge 模块”   。

3. 为 IoT Edge 模块提供有效的名称和映像 URI。
   可以在本教程第一部分中创建的资源组的容器注册表中找到映像 URI。 选择“服务”下的“存储库”部分。 对于本教程，请选择名为 `silicaprediction` 的存储库。 选择相应标记。 映像 URI 的格式为：

   containerregistry 登录服务器/存储库名称:标记名称

   例如：

   ```
   ASEdemocontregistry.azurecr.io/silicaprediction:amd64
   ```

4. 按原样保留“重启策略”和“所需状态”字段 。

5. 单击“添加”  。


## <a name="deploy-the-azure-sql-edge-module"></a>部署 Azure SQL Edge 模块

1. 通过依次单击“+ 添加”和“市场模块”部署 Azure SQL Edge 模块 。 

2. 在“IoT Edge 模块市场”边栏选项卡上，搜索“Azure SQL Edge”并选择“Azure SQL Edge 开发人员” 。 

3. 单击“IoT Edge 模块”下新添加的“Azure SQL Edge”模块，以配置 Azure SQL Edge 模块。 有关配置选项的详细信息，请参阅[部署 Azure SQL Edge](https://docs.microsoft.com/azure/azure-sql-edge/deploy-portal)。

4. 将 `MSSQL_PACKAGE` 环境变量添加到 Azure SQL Edge 模块部署中，并指定在本教程[第一部分](tutorial-deploy-azure-resources.md)的步骤 8 中创建的数据库 dacpac 文件的 SAS URL。

5. 单击“更新”

6. 在“在设备上设置模块”页上 **，单击“下一步：路由 >”** 。

7. 在“在设备上设置模块”页的路由窗格上，按如下所述为模块指定到 IoT Edge 中心通信的路由。 请确保更新下面路由定义中的模块名称。

   ```
   FROM /messages/modules/<your_data_generator_module>/outputs/IronOreMeasures INTO
   BrokeredEndpoint("/modules/<your_azure_sql_edge_module>/inputs/IronOreMeasures")
   ```

   例如：

   ```
   FROM /messages/modules/ASEDataGenerator/outputs/IronOreMeasures INTO BrokeredEndpoint("/modules/AzureSQLEdge/inputs/IronOreMeasures")
   ```


7. 在“在设备上设置模块”页上 **，单击“下一步：查看 + 创建 >”**

8. 在“在设备上设置模块”页上，单击“创建” 

## <a name="create-and-start-the-t-sql-streaming-job-in-azure-sql-edge"></a>在 Azure SQL Edge 中创建并启动 T-SQL 流式处理作业。

1. 打开 Azure Data Studio。

2. 在“欢迎使用”选项卡中，使用以下详细信息启动一个新连接：

   |_字段_|_值_|
   |-------|-------|
   |连接类型| Microsoft SQL Server|
   |服务器|为此演示创建的 VM 中提及的公共 IP 地址|
   |用户名|sa|
   |密码|创建 Azure SQL Edge 实例时使用的强密码|
   |数据库|默认|
   |服务器组|默认|
   |名称（可选）|提供可选名称|

3. 单击“连接”

4. 在“文件”菜单选项卡中，打开一个新笔记本或使用键盘快捷方式 Ctrl + N。

5. 在新的查询窗口中，执行以下脚本以创建 T-SQL 流式处理作业。 在执行脚本之前，请确保更改以下变量。 
   - SQL_SA_Password：部署 Azure SQL Edge 模块时指定的 MSSQL_SA_PASSWORD 值。 
   
   ```sql
   Use IronOreSilicaPrediction
   Go

   Declare @SQL_SA_Password varchar(200) = '<SQL_SA_Password>'
   declare @query varchar(max) 

   /*
   Create Objects Required for Streaming
   */

   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'MyStr0ng3stP@ssw0rd';

   If NOT Exists (select name from sys.external_file_formats where name = 'JSONFormat')
   Begin
      CREATE EXTERNAL FILE FORMAT [JSONFormat]  
      WITH ( FORMAT_TYPE = JSON)
   End 


   If NOT Exists (select name from sys.external_data_sources where name = 'EdgeHub')
   Begin
      Create EXTERNAL DATA SOURCE [EdgeHub] 
      With(
         LOCATION = N'edgehub://'
      )
   End 

   If NOT Exists (select name from sys.external_streams where name = 'IronOreInput')
   Begin
      CREATE EXTERNAL STREAM IronOreInput WITH 
      (
         DATA_SOURCE = EdgeHub,
         FILE_FORMAT = JSONFormat,
         LOCATION = N'IronOreMeasures'
       )
   End


   If NOT Exists (select name from sys.database_scoped_credentials where name = 'SQLCredential')
   Begin
       set @query = 'CREATE DATABASE SCOPED CREDENTIAL SQLCredential
                 WITH IDENTITY = ''sa'', SECRET = ''' + @SQL_SA_Password + ''''
       Execute(@query)
   End 

   If NOT Exists (select name from sys.external_data_sources where name = 'LocalSQLOutput')
   Begin
      CREATE EXTERNAL DATA SOURCE LocalSQLOutput WITH (
      LOCATION = 'sqlserver://tcp:.,1433',CREDENTIAL = SQLCredential)
   End

   If NOT Exists (select name from sys.external_streams where name = 'IronOreOutput')
   Begin
      CREATE EXTERNAL STREAM IronOreOutput WITH 
      (
         DATA_SOURCE = LocalSQLOutput,
         LOCATION = N'IronOreSilicaPrediction.dbo.IronOreMeasurements'
      )
   End

   EXEC sys.sp_create_streaming_job @name=N'IronOreData',
   @statement= N'Select * INTO IronOreOutput from IronOreInput'

   exec sys.sp_start_streaming_job @name=N'IronOreData'
   ```

6. 使用以下查询验证数据生成器模块中的数据是否正在流式传输到数据库中。 

   ```sql
   Select Top 10 * from dbo.IronOreMeasurements
   order by timestamp desc
   ```


在本教程中，我们部署了数据生成器模块和 SQL Edge 模块。 然后，我们创建了一个流式处理作业，将数据生成器模块生成的数据流式传输到 SQL。 

## <a name="next-steps"></a>后续步骤

- [使用 ONNX 在 Azure SQL Edge 上部署 ML 模型](tutorial-run-ml-model-on-sql-edge.md)
