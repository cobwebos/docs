---
title: 使用 Azure 门户部署 SQL 数据库边缘预览 |Microsoft Docs
description: 了解如何使用 Azure 门户部署 Azure SQL 数据库边缘
keywords: 部署 sql 数据库边缘
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 9da756b702c994d69aae42ecef0e2da4d44eed39
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514090"
---
# <a name="deploy-azure-sql-database-edge-preview"></a>部署 Azure SQL 数据库边缘预览

Azure SQL 数据库边缘预览版是为 IoT 和 Azure IoT Edge 部署优化的关系数据库引擎。 它提供为 IoT 应用程序和解决方案创建高性能数据存储和处理层的功能。 本快速入门介绍如何开始使用 Azure 门户 Azure IoT Edge 创建 Azure SQL 数据库边缘模块。

## <a name="before-you-begin"></a>开始之前

* 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/)。
* 登录到 [Azure 门户](https://portal.azure.com/)。
* [在此处](https://azure.microsoft.com/services/sql-database-edge/#contact)提交请求，以便为 SQL 数据库边缘部署启用订阅。
* 创建[Azure IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。
* [从 Azure 门户注册 IoT Edge 设备](../iot-edge/how-to-register-device-portal.md)。
* 准备 IoT Edge 设备以便[从 Azure 门户部署 IoT Edge 模块](../iot-edge/how-to-deploy-modules-portal.md)。

> [!NOTE]
> 若要将 Azure Linux VM 部署为 IoT Edge 设备，请参阅此[快速入门指南](../iot-edge/quickstart-linux.md)。

## <a name="deploy-sql-database-edge-module-from-azure-marketplace"></a>部署 Azure Marketplace 中的 SQL 数据库边缘模块

Azure 市场是一个应用程序和服务在线市场，可在其中浏览各种企业应用程序和解决方案，这些应用程序和解决方案针对在 Azure 上运行进行了认证和优化，包括 [IoT Edge 模块](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)。 可以通过 marketplace 将 Azure SQL 数据库边缘部署到边缘设备。

1. 在 Azure Marketplace 中找到 Azure SQL 数据库边缘模块。<br><br>

   ![MarketPlace 中的 SQL 数据库边缘](media/deploy-portal/find-offer-marketplace.png)

2. 选择最符合要求的软件计划，并单击 "**创建**"。 <br><br>

   ![选择正确的软件计划](media/deploy-portal/pick-correct-plan.png)

3. 在 "IoT Edge 模块的目标设备" 页上，指定以下详细信息，然后单击 "**创建**"

   |**字段**  |**说明**  |
   |---------|---------|
   |订阅  |  创建 IoT 中心所用的 Azure 订阅 |
   |IoT 中心   |  注册 IoT Edge 设备的 IoT 中心的名称，然后选择 "部署到设备" 选项|
   |IoT Edge 设备名称  |  部署 SQL 数据库边缘 IoT Edge 设备的名称 |

4. 在 "**设置模块**" 页上，导航到 "部署模块" 部分，然后针对 SQL 数据库边缘模块单击 "**配置**"。 

5. 在 " **IoT Edge 自定义模块**" 窗格上，为环境变量指定所需值，并/或自定义模块的 "创建选项" 和 "所需属性"。 有关受支持的环境变量的完整列表，请参阅[SQL Server 容器环境变量](/sql/linux/sql-server-linux-configure-environment-variables/)。

   |**Parameter**  |**说明**|
   |---------|---------|
   | 名称 | 模块的名称。 |
   |SA_PASSWORD  | 为 SQL 数据库边缘管理员帐户指定强密码。 |
   |MSSQL_LCID   | 设置要用于 SQL Server 的语言 ID。 例如，1036为法语。 |
   |MSSQL_COLLATION | 设置 SQL Server 的默认排序规则。 此设置将替代语言 ID （LCID）到排序规则的默认映射。 |

   > [!NOTE]
   > 请不要更改或更新该模块上的**图像 URI**或**ACCEPT_EULA**设置。

6. 在 " **IoT Edge 自定义模块**" 窗格上，为**主机端口**和安装点的**目标**更新容器 "创建选项" 所需的值。 装载点目标是 SQL 数据库文件在主机 IoT Edge 设备上的存储位置。

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

7. 在 " **IoT Edge 自定义模块**" 窗格上，*将 Set module 克隆的所需属性*更新为包含 SQL 包的位置和流分析作业信息。 这两个字段是可选的，如果要使用数据库和流式处理作业部署 SQL 数据库边缘模块，则应使用此字段。

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

8. 在 " **IoT Edge 自定义模块**" 窗格上，将 "*重新启动策略*" 设置为 "始终" 和 "*所需状态*"
9. 在**IoT Edge 自定义模块**"窗格上，单击"**保存**"。
10. 在 "**设置模块**" 页上，单击 "**下一步**"。
11. 在 "**设置模块**的**指定路由（可选）** " 页上，指定 "模块到模块" 或 "模块到 IoT Edge 集线器通信的路由"，请参阅[部署模块并在 IoT Edge 中建立路由](../iot-edge/module-composition.md)。
12. 单击“下一步”。
13. 单击“提交”。

本快速入门介绍了如何在 IoT Edge 设备上部署 SQL 数据库边缘模块。

## <a name="next-steps"></a>后续步骤

- [在 SQL 数据库边缘机器学习和人工智能与 ONNX](onnx-overview.md)。
- 使用 IoT Edge 通过 SQL 数据库边缘构建端到端 IoT 解决方案。