---
title: 在 Azure SQL Edge 中设置 IoT Edge 模块
description: 本 Azure SQL Edge 铁矿石杂质预测教程由三部分组成，在第二部分中，你将设置 IoT Edge 模块和连接。
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: bbbbe09aac30165a2f9b7bbe54f58e0c09a6cf09
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593495"
---
# <a name="set-up-iot-edge-modules-and-connections"></a>设置 IoT Edge 模块和连接

本 Azure SQL Edge 铁矿石杂质预测教程由三部分组成，在第二部分中，你将设置以下 IoT Edge 模块：

- Azure SQL Edge
- 数据生成器 IoT Edge 模块

## <a name="create-azure-stream-analytics-module"></a>创建 Azure 流分析模块

创建将在本教程中使用的 Azure 流分析模块。 若要详细了解如何在 SQL Edge 中使用流式处理作业，请参阅[在 SQL Database Edge 中使用流式处理作业 ](https://docs.microsoft.com/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge)。

创建 Azure 流分析作业，并将托管环境设置为 Edge 后，应设置该教程的输入和输出。

1. 若要创建“输入”，请单击“+ 添加流输入”。 使用以下信息填写详细信息部分：

   字段|值
   -----|-----
   事件序列化格式|JSON
   编码|UTF-8
   事件压缩类型|无

2. 若要创建“输出”，请单击“+ 添加”，然后选择 SQL 数据库。 使用以下信息填写详细信息部分：

   > [!NOTE]
   > 在“部署 Azure SQL Edge 模块”部分中部署 SQL Edge 模块时，需要在此部分中为 SQL SA 指定密码。

   字段|值
   -----|-----
   数据库|IronOreSilicaPrediction
   服务器名称|tcp:.,1433
   用户名|sa
   密码|指定强密码
   表|IronOreMeasurements1

3. 导航至“查询”部分，然后按以下步骤设置查询：

   `SELECT * INTO <name_of_your_output_stream> FROM <name_of_your_input_stream>`
   
4. 在“配置”下，选择“发布”，然后选择“发布”按钮。 保存 SAS URI 以用于 SQL Database Edge 模块。

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

1. 在“IoT Edge 模块” 部分中，单击“+ 添加”并选择“IoT Edge 模块”。

2. 提供 IoT Edge 模块名称和映像 URI。
   可在资源组中的容器注册表中找到映像 URI。 选择“服务”下的“存储库”部分。 对于本教程，请选择名为 `silicaprediction` 的存储库。 选择相应标记。 映像 URI 的格式为：

   containerregistry 登录服务器/存储库名称:标记名称

   例如：

   ```
   ASEdemocontregistry.azurecr.io/silicaprediction:amd64
   ```

3. 单击“添加”。

## <a name="deploy-the-azure-sql-edge-module"></a>部署 Azure SQL Edge 模块

1. 按照[部署 Azure SQL Database Edge 预览版](https://docs.microsoft.com/azure/sql-database-edge/deploy-portal#deploy-sql-database-edge)中列出的步骤来部署 Azure SQL Database Edge 模块。

2. 在“设置模块”页面的“指定路由”上，按如下所示为模块指定到 IoT Edge 中心通信的路由。 

   ```
   FROM /messages/modules/<your_data_generator_module>/outputs/<your_output_stream_name> INTO
   BrokeredEndpoint("/modules/<your_azure_sql_edge_module>/inputs/<your_input_stream_name>")
   ```

   例如：

   ```
   FROM /messages/modules/ASEDataGenerator/outputs/IronOreMeasures INTO BrokeredEndpoint("/modules/AzureSQLEdge/inputs/Input1")
   ```

3. 在“模块孪生”设置中，确保 SQLPackage 和 ASAJonInfo 通过在本教程前面保存的相关 SAS URL 进行更新。

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

## <a name="next-steps"></a>后续步骤

- [使用 ONNX 在 Azure SQL Edge 上部署 ML 模型](tutorial-run-ml-model-on-sql-edge.md)
