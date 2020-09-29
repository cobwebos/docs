---
title: 使用 Azure 门户部署 Azure SQL Edge
description: 了解如何使用 Azure 门户部署 Azure SQL Edge
keywords: 部署 SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 683a3fc148d3521366beef4427e87200ea81a9f3
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91445730"
---
# <a name="deploy-azure-sql-edge"></a>部署 Azure SQL Edge 

Azure SQL Edge 是已优化的关系数据库引擎，更适合 IoT 和 Azure IoT Edge 部署。 它提供了为 IoT 应用和解决方案创建高性能数据存储和处理层的功能。 本快速入门介绍了如何开始在 Azure 门户中使用 Azure IoT Edge 创建 Azure SQL Edge 模块。

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

4. 在 "在 **设备上设置模块：** " 页上，单击 " **IoT Edge 模块**" 下的 "Azure SQL Edge" 模块。 默认模块名称设置为 *AzureSQLEdge*。 

5. 在 "**更新 IoT Edge 模块**" 边栏选项卡的 "*模块设置*" 部分，为 " *IoT Edge 模块名称*"、"*重新启动策略*" 和 "*所需状态*" 指定所需的值。 

   > [!IMPORTANT]    
   > 不要更改或更新模块上的 **图像 URI** 设置。

6. 在 "**更新 IoT Edge 模块**" 边栏选项卡的 "*环境变量*" 部分，为环境变量指定所需的值。 有关 Azure SQL Edge 环境变量的完整列表，请参阅 [使用环境变量进行配置](configure.md#configure-by-using-environment-variables)。 为模块定义了以下默认环境变量。 

   |**参数**  |**说明**|
   |---------|---------|
   | MSSQL_SA_PASSWORD  | 更改默认值，为 SQL Edge 管理员帐户指定强密码。 |
   | MSSQL_LCID   | 更改默认值以设置要用于 SQL Edge 的所需语言 ID。 例如，1036 为法语。 |
   | MSSQL_COLLATION | 更改默认值以设置 SQL Edge 的默认排序规则。 此设置替代语言 ID (LCID) 到排序规则的默认映射。 |

   > [!IMPORTANT]    
   > 请勿更改或更新模块的 **ACCEPT_EULA** 环境变量。

7. 在 "**更新 IoT Edge 模块**" 边栏选项卡上的 "*容器创建选项*" 部分，按要求更新以下选项。 
   - **主机端口：** 将指定的主机端口映射到端口 1433 (容器中的默认 SQL 端口) 。
   - **绑定** 和 **装载：** 如果需要部署多个 SQL Edge 模块，请确保更新装载选项，为永久性卷创建新源 & 目标对。 若要详细了解装载和卷，请参阅 docker 文档中的[使用卷](https://docs.docker.com/storage/volumes/)。 

   ```json
   {
    "HostConfig": {
        "CapAdd": [
            "SYS_PTRACE"
        ],
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
        "ClientTransportType=AMQP_TCP_Only",
        "PlanId=asde-developer-on-iot-edge"
    ]
   }
   ```
   > [!IMPORTANT]    
   > 不要更改 `PlanId` 在创建配置设置中定义的环境变量。 如果更改此值，则 Azure SQL Edge 容器将无法启动。 
   
8. 在 " **更新 IoT Edge 模块** " 窗格上，单击 " **更新**"。
9. 在 " **设置设备上的模块** " 页上，单击 " **下一步：路由" >** 如果需要为部署定义路由。 否则，单击 " **查看 + 创建**"。 有关配置路由的详细信息，请参阅 [在 IoT Edge 中部署模块和建立路由](../iot-edge/module-composition.md)。
11. 在 " **设置设备上的模块** " 页上，单击 " **创建**"。

## <a name="connect-to-azure-sql-edge"></a>连接到 Azure SQL Edge

下列步骤在容器内部使用 Azure SQL Edge 命令行工具 sqlcmd 来连接 Azure SQL Edge。

> [!NOTE]      
>  (sqlcmd) 的 SQL 命令行工具在 Azure SQL Edge 容器的 ARM64 版本中不可用。

1. 使用 `docker exec -it` 命令在运行的容器内部启动交互式 Bash Shell。 在下面的示例中 `azuresqledge` ，是由 IoT Edge 模块的参数指定的名称 `Name` 。

   ```bash
   sudo docker exec -it azuresqledge "bash"
   ```

2. 在容器内部使用 sqlcmd 进行本地连接。 默认情况下，sqlcmd 不在路径之中，因此需要指定完整路径。

   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "<YourNewStrong@Passw0rd>"
   ```

   > [!TIP]    
   > 可以省略命令行上提示要输入的密码。

3. 如果成功，应会显示 sqlcmd 命令提示符：`1>`。

## <a name="create-and-query-data"></a>创建和查询数据

以下部分将引导你使用 sqlcmd 和 Transact-SQL 完成新建数据库、添加数据并运行查询的整个过程。

### <a name="create-a-new-database"></a>新建数据库

以下步骤创建一个名为 `TestDB` 的新数据库。

1. 在 sqlcmd 命令提示符中，粘贴以下 Transact-SQL 命令以创建测试数据库：

   ```sql
   CREATE DATABASE TestDB
   Go
   ```

2. 在下一行中，编写一个查询以返回服务器上所有数据库的名称：

   ```sql
   SELECT Name from sys.Databases
   Go
   ```

### <a name="insert-data"></a>插入数据

接下来创建一个新表 `Inventory`，然后插入两个新行。

1. 在 sqlcmd 命令提示符中，将上下文切换到新的 `TestDB` 数据库：

   ```sql
   USE TestDB
   ```

2. 创建名为 `Inventory` 的新表：

   ```sql
   CREATE TABLE Inventory (id INT, name NVARCHAR(50), quantity INT)
   ```

3. 将数据插入新表：

   ```sql
   INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (2, 'orange', 154);
   ```

4. 要执行上述命令的类型 `GO`：

   ```sql
   GO
   ```

### <a name="select-data"></a>选择数据

现在，运行查询以从 `Inventory` 表返回数据。

1. 通过 sqlcmd 命令提示符输入查询，以返回 `Inventory` 表中数量大于 152 的行：

   ```sql
   SELECT * FROM Inventory WHERE quantity > 152;
   ```

2. 执行此命令：

   ```sql
   GO
   ```

### <a name="exit-the-sqlcmd-command-prompt"></a>退出 sqlcmd 命令提示符

1. 要结束 sqlcmd 会话，请键入 `QUIT`：

   ```sql
   QUIT
   ```

2. 要在容器中退出交互式命令提示，请键入 `exit`。 退出交互式 Bash Shell 后，容器将继续运行。

## <a name="connect-from-outside-the-container"></a> 从容器外连接

你可以从支持 SQL 连接的任何外部 Linux、Windows 或 macOS 工具连接到 Azure SQL Edge 实例，并对其运行 SQL 查询。 有关从外部连接到 SQL Edge 容器的详细信息，请参阅[连接和查询 Azure SQL Edge](https://docs.microsoft.com/azure/azure-sql-edge/connect)。

在本快速入门中，你在 IoT Edge 设备上部署了 SQL Edge 模块。 

## <a name="next-steps"></a>后续步骤

- [SQL Edge 中的 ONNX 与的智能机器学习](onnx-overview.md)
- [使用 IoT Edge 通过 SQL Edge 构建端到端 IoT 解决方案](tutorial-deploy-azure-resources.md)
- [Azure SQL Edge 中的数据流式处理](stream-data.md)
- [排查部署错误](troubleshoot.md)
