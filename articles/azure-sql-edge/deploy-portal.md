---
title: 使用 Azure 门户部署 Azure SQL Edge（预览）
description: 了解如何使用 Azure 门户部署 Azure SQL Edge（预览）
keywords: 部署 SQL Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 7dfc7b680c6b7d6b2a3641b25c0f82665143bde1
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594666"
---
# <a name="deploy-azure-sql-edge-preview"></a>部署 Azure SQL Edge（预览） 

Azure SQL Edge（预览）是已优化的关系数据库引擎，更适合 IoT 和 Azure IoT Edge 部署。 它提供了为 IoT 应用和解决方案创建高性能数据存储和处理层的功能。 本快速入门介绍了如何开始在 Azure 门户中使用 Azure IoT Edge 创建 Azure SQL Edge 模块。

## <a name="before-you-begin"></a>开始之前

* 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/)。
* 登录 [Azure 门户](https://portal.azure.com/)。
* 创建 [Azure IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。
* [在 Azure 门户中注册 IoT Edge 设备](../iot-edge/how-to-register-device-portal.md)。
* 准备 IoT Edge 设备，以[在 Azure 门户中部署 IoT Edge 模块](../iot-edge/how-to-deploy-modules-portal.md)。

> [!NOTE]
> 若要将 Azure Linux VM 部署为 IoT Edge 设备，请参阅这篇[快速入门指南](../iot-edge/quickstart-linux.md)。

## <a name="deploy-sql-edge-module-from-azure-marketplace"></a>从 Azure 市场部署 SQL Edge 模块

Azure 市场是一个应用程序和服务在线市场，可在其中浏览各种企业应用程序和解决方案，这些应用程序和解决方案针对在 Azure 上运行进行了认证和优化，包括 [IoT Edge 模块](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)。 Azure SQL Edge 可以通过市场部署到边缘设备。

1. 在 Azure 市场中找到 Azure SQL Edge 模块。<br><br>

   ![市场中的 SQL Edge](media/deploy-portal/find-offer-marketplace.png)

2. 选择最能满足你需求的软件计划，然后单击“创建”。 <br><br>

   ![选择正确的软件计划](media/deploy-portal/pick-correct-plan.png)

3. 在“IoT Edge 模块的目标设备”页上，指定以下详细信息，然后单击“创建”

   |**字段**  |**说明**  |
   |---------|---------|
   |订阅  |  在其中创建了 IoT 中心的 Azure 订阅 |
   |IoT 中心   |  输入在其中注册了 IoT Edge 设备的 IoT 中心的名称，然后选择“部署到设备”选项|
   |IoT Edge 设备名称  |  要在其中部署 SQL Edge 的 IoT Edge 设备的名称 |

4. 在“设置模块”页上，转到部署模块部分，然后单击 SQL Edge 模块对应的“配置”。 

5. “在 IoT Edge 自定义模块”窗格上，为环境变量指定所需值，并/或自定义模块的创建选项和所需属性。 有关受支持的环境变量的完整列表，请参阅 [SQL Server 容器环境变量](/sql/linux/sql-server-linux-configure-environment-variables/)。

   |**Parameter**  |**说明**|
   |---------|---------|
   | 名称 | 模块名称。 |
   |SA_PASSWORD  | 为 SQL Edge 管理员帐户指定强密码。 |
   |MSSQL_LCID   | 设置用于 SQL Server 的语言 ID。 例如，1036 为法语。 |
   |MSSQL_COLLATION | 设置 SQL Server 的默认排序规则。 此设置替代语言 ID (LCID) 到排序规则的默认映射。 |

   > [!NOTE]
   > 请不要更改或更新模块上的“映像 URI”或“ACCEPT_EULA”设置。

6. 在“IoT Edge 自定义模块”窗格上，为“主机端口”更新容器创建选项的所需值。 如需部署多个 SQL DB Edge 模块，请务必更新装载选项，以便为永久性卷新建源和目标对。 若要详细了解装载和卷，请参阅 docker 文档中的[使用卷](https://docs.docker.com/storage/volumes/)。 

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

7. 在“IoT Edge 自定义模块”窗格上，更新“设置模块孪生的所需属性”，以包括 SQL 包的位置和流分析作业信息。 这两个字段是可选的，如果要部署带有数据库和流式处理作业的 SQL Edge 模块，就应该使用它们。

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

8. 在“IoT Edge 自定义模块”窗格上，将“重启策略”设置为“始终”，并将“所需状态”设置为“正在运行”。
9. 在“IoT Edge 自定义模块”窗格上，单击“保存”。
10. 在“设置模块”页上，单击“下一步”。
11. 在“设置模块”的“指定路由(可选)”页上，指定“模块到模块”或“模块到 IoT Edge 中心通信”路由（请参阅[在 IoT Edge 中部署模块并建立路由](../iot-edge/module-composition.md)）。
12. 单击“下一步”。
13. 单击“提交”。

在本快速入门中，你在 IoT Edge 设备上部署了 SQL Edge 模块。

## <a name="next-steps"></a>后续步骤

- [在 SQL Edge 中将机器学习和人工智能与 ONNX 结合使用](onnx-overview.md)。
- 使用 IoT Edge 通过 SQL Edge 生成端到端 IoT 解决方案。