---
title: 连接和查询 Azure SQL Edge
description: 了解如何连接和查询 Azure SQL Edge。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/25/2020
ms.openlocfilehash: 4548d4956b4cd01886fb1be9a530cc1627f76b2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90888236"
---
# <a name="connect-and-query-azure-sql-edge"></a>连接和查询 Azure SQL Edge

在 Azure SQL Edge 中，在部署容器后，可以从以下任意位置连接到数据库引擎：

- 在容器内部
- 从另一个 Docker 容器（在同一主机上运行）
- 从主机
- 从网络上的任何其他客户端计算机

## <a name="tools-to-connect-to-azure-sql-edge"></a>连接到 Azure SQL Edge 的工具

可以通过以下任一常用工具连接到 Azure SQL Edge 实例：

* [sqlcmd](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools)：sqlcmd 客户端工具已包含在 Azure SQL Edge 的容器映像中。 如果使用交互式 bash shell 附加到正在运行的容器，则可以在本地运行这些工具。 SQL 客户端工具在 ARM64 平台上不可用，因为 SQL Edge 容器的 ARM64 版本中不包含这些工具。 
* [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)
* [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio)
* [Visual Studio Code](https://docs.microsoft.com/sql/visual-studio-code/sql-server-develop-use-vscode)

若要从网络计算机连接到 Azure SQL Edge 数据库引擎，你需要以下各项：

- **主机的 IP 地址或网络名称**：这是运行 Azure SQL Edge 容器的主机。
- **Azure SQL Edge 容器主机端口映射**：这是 Docker 容器端口到主机端口的映射。 在容器内，Azure SQL Edge 始终映射到端口 1433。 如果需要，你可以更改此端口。 若要更改端口号，请在 Azure IoT Edge 中更新 Azure SQL Edge 模块的“容器创建选项”。 在下面的示例中，容器上的端口 1433 映射到主机上的端口 1600。

    ```JSON
    {
        "PortBindings": {
          "1433/tcp": [
            {
              "HostPort": "1600"
            }
          ]
        }
    }
    ```

- **Azure SQL Edge 实例的 SA 密码**：这是在部署 Azure SQL Edge 期间为 `SA_PASSWORD` 环境变量指定的值。

## <a name="connect-to-the-database-engine-from-within-the-container"></a>从容器内连接到数据库引擎

[SQL Server 命令行工具](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools)包括在 Azure SQL Edge 的容器映像中。 如果使用交互式命令提示符进行附加到此容器的操作，则可在本地运行这些工具。 SQL 客户端工具在 ARM64 平台上不可用，因为 SQL Edge 容器的 ARM64 版本中不包含这些工具。 

1. 使用 `docker exec -it` 命令在运行的容器内部启动交互式 Bash Shell。 在下面的示例中，`e69e056c702d` 是容器 ID。

    ```bash
    docker exec -it <Azure SQL Edge container ID or name> /bin/bash
    ```

    > [!TIP]
    > 并非始终需要指定完整的容器 ID。 只需指定能够唯一标识它的足够字符即可。 因此，在本示例中，使用 `e6` 或 `e69` 足矣，无需使用完整 ID。

2. 当在容器内部时，使用 sqlcmd 在本地进行连接。 默认情况下，sqlcmd 不在路径之中，因此需要指定完整路径。

    ```bash
    /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P '<YourPassword>'
    ```

3. 使用 sqlcmd 完成操作后，键入 `exit`。

4. 使用交互式命令提示符完成操作后，键入 `exit`。 退出交互式 Bash Shell 后，容器将继续运行。

## <a name="connect-to-azure-sql-edge-from-another-container-on-the-same-host"></a>从同一主机上的另一个容器连接到 Azure SQL Edge

由于在同一主机上运行的两个容器位于同一 Docker 网络上，因此可以使用该服务的容器名称和端口地址轻松访问这些容器。 例如，如果从同一主机上的另一个 Python 模块（容器）连接到 Azure SQL Edge 的实例，则可以使用类似于以下内容的连接字符串。 （此示例假定 Azure SQL Edge 配置为在默认端口上侦听。）

```python

import pyodbc
server = 'MySQLEdgeContainer' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

## <a name="connect-to-azure-sql-edge-from-another-network-machine"></a>从另一台网络计算机连接到 Azure SQL Edge

你可能需要从网络上的另一台计算机连接到 Azure SQL Edge 的实例。 为此，请使用 Docker 主机的 IP 地址和 Azure SQL Edge 容器映射到的主机端口。 例如，如果 Docker 主机的 IP 地址是 xxx.xxx.xxx.xxx，并且 Azure SQL Edge 容器映射到主机端口 1600，则 Azure SQL Edge 实例的服务器地址将是 xxx.xxx.xxx.xxx,1600。 更新后的 Python 脚本为：

```python

import pyodbc
server = 'xxx.xxx.xxx.xxx,1600' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

若要使用在 Windows 计算机上运行的 SQL Server Management Studio 连接到 Azure SQL Edge 实例，请参阅 [SQL Server Management Studio](https://docs.microsoft.com/sql/linux/sql-server-linux-manage-ssms)。

若要使用 Windows、Mac 或 Linux 计算机上的 Visual Studio Code 连接到 Azure SQL Edge 实例，请参阅 [Visual Studio Code](https://docs.microsoft.com/sql/visual-studio-code/sql-server-develop-use-vscode)。

若要使用 Windows、Mac 或 Linux 计算机上的 Azure Data Studio 连接到 Azure SQL Edge 实例，请参阅 [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-server)。

## <a name="next-steps"></a>后续步骤

[连接和查询](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#connect-and-query)

[在 Linux 上安装 SQL Server 工具](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools)
