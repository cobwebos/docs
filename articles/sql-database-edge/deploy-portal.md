---
title: 使用 Azure 门户部署 SQL 数据库边缘预览 |微软文档
description: 了解如何使用 Azure 门户部署 Azure SQL 数据库边缘
keywords: 部署 sql 数据库边缘
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 9da922de38d820864b3f83de80fe64eb3ac792e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246715"
---
# <a name="deploy-azure-sql-database-edge-preview"></a>部署 Azure SQL 数据库边缘预览

Azure SQL 数据库边缘预览是一个关系数据库引擎，专为 IoT 和 Azure IoT 边缘部署进行了优化。 它提供了为 IoT 应用程序和解决方案创建高性能数据存储和处理层的功能。 此快速入门演示如何开始使用 Azure 门户通过 Azure IoT 边缘创建 Azure SQL 数据库边缘模块。

## <a name="before-you-begin"></a>开始之前

* 如果没有 Azure 订阅，请创建[一个免费帐户](https://azure.microsoft.com/free/)。
* 登录到 Azure[门户](https://portal.azure.com/)。
* [在此处](https://azure.microsoft.com/services/sql-database-edge/#contact)提交请求，以启用用于部署 SQL 数据库边缘的订阅。
* 创建[Azure IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。
* 从[Azure 门户注册 IoT 边缘设备](../iot-edge/how-to-register-device-portal.md)。
* 准备 IoT 边缘设备以[从 Azure 门户部署 IoT 边缘模块](../iot-edge/how-to-deploy-modules-portal.md)。

> [!NOTE]
> 要将 Azure Linux VM 部署为 IoT 边缘设备，请参阅此[快速入门指南](../iot-edge/quickstart-linux.md)。

## <a name="deploy-sql-database-edge-module-from-azure-marketplace"></a>从 Azure 应用商店部署 SQL 数据库边缘模块

Azure 市场是一个应用程序和服务在线市场，可在其中浏览各种企业应用程序和解决方案，这些应用程序和解决方案针对在 Azure 上运行进行了认证和优化，包括 [IoT Edge 模块](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)。 Azure SQL 数据库边缘可以通过市场部署到边缘设备。

1. 在 Azure 应用商店中查找 Azure SQL 数据库边缘模块。<br><br>

   ![市场中的 SQL 数据库边缘](media/deploy-portal/find-offer-marketplace.png)

2. 选择最符合您要求的软件计划，然后单击"**创建**"。 <br><br>

   ![选择正确的软件计划](media/deploy-portal/pick-correct-plan.png)

3. 在 IoT 边缘模块的目标设备页面上，指定以下详细信息，然后单击"**创建"**

   |**字段**  |**说明**  |
   |---------|---------|
   |订阅  |  创建 IoT 中心下的 Azure 订阅 |
   |IoT 中心   |  注册 IoT 边缘设备的 IoT 中心的名称，然后选择"部署到设备"选项|
   |IoT 边缘设备名称  |  将部署 SQL 数据库边缘的 IoT 边缘设备的名称 |

4. 在 **"设置模块"** 页上，导航到有关部署模块的部分，然后单击 SQL 数据库边缘模块的 **"配置**"。 

5. 在**IoT 边缘自定义模块**窗格中，指定环境变量的所需值和/或自定义模块的创建选项和所需属性。 有关受支持环境变量的完整列表，请参阅[SQL Server 容器环境变量](/sql/linux/sql-server-linux-configure-environment-variables/)。

   |**参数**  |**说明**|
   |---------|---------|
   | “属性” | 模块的名称。 |
   |SA_PASSWORD  | 为 SQL 数据库边缘管理员帐户指定强密码。 |
   |MSSQL_LCID   | 设置用于 SQL Server 的语言 ID。 例如，1036 是法语。 |
   |MSSQL_COLLATION | 设置 SQL Server 的默认排序规则。 此设置将覆盖语言 ID （LCID） 到排序的默认映射。 |

   > [!NOTE]
   > 请不要更改或更新**模块上的映像 URI**或**ACCEPT_EULA**设置。

6. 在**IoT 边缘自定义模块**窗格中，更新容器为**主机端口**创建所需的值选项。 如果需要部署多个 SQL DB Edge 模块，请确保更新装载选项，以创建持久卷的新源&目标对。 有关装载和卷的详细信息，请参阅 Docker 文档中[的"使用卷](https://docs.docker.com/storage/volumes/)"。 

   ```json
       {
         "HostConfig": {
           "Binds": [
             "sqlvolume:/sqlvolume"
           ],
           "PortBindings": {
             "1433/tcp": [
               {
                 "HostPort": "1433"
               }
             ]
           },
           "Mounts": [
             {
               "Type": "volume",
               "Source": "sqlvolume",
               "Target": "/var/opt/mssql"
             }
           ]
         },
         "Env": [
           "MSSQL_AGENT_ENABLED=TRUE",
           "MSSQL_PID=Developer"
         ]
       }
   ```

7. 在**IoT 边缘自定义模块**窗格中，更新 *"设置"模块孪生的所需属性*，以包括 SQL 包的位置和流分析作业信息。 这两个字段是可选的，如果要使用数据库和流式处理作业部署 SQL 数据库边缘模块，则应使用。

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

8. 在**IoT 边缘自定义模块**窗格中，将 *"重新启动策略*"设置为"始终 *"，并将"所需状态*"设置为"始终运行"。
9. 在**IoT 边缘自定义模块**窗格中，单击"**保存**"。
10. 在 **"设置模块"** 页上单击 **"下一步**"。
11. 在 **"设置模块"** 页的 **"指定路由（可选"）** 上，指定模块到模块或模块到 IoT 边缘中心通信的路由，请参阅[部署模块并在 IoT 边缘中建立路由](../iot-edge/module-composition.md)。
12. 单击“下一步”****。
13. 单击 **“提交”**。

在此快速入门中，您将 SQL 数据库边缘模块部署在 IoT 边缘设备上。

## <a name="next-steps"></a>后续步骤

- [机器学习和人工智能与 SQL 数据库边缘中的 ONNX。](onnx-overview.md)
- 使用 IoT 边缘使用 SQL 数据库边缘构建端到端 IoT 解决方案。
